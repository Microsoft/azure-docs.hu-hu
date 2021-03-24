---
title: Application Gateway Azure biztonsági alapterve
description: A Application Gateway biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: application-gateway
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4fc26d8e154dc0a58bb3436a8161d2e21efa2b86
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952014"
---
# <a name="azure-security-baseline-for-application-gateway"></a>Application Gateway Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) Application Gatewayre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Application Gateway vonatkozó kapcsolódó útmutatás. A Application Gateway nem alkalmazható **vezérlők** ki vannak zárva.

 
Ha szeretné megtekinteni, hogyan Application Gateway teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Application Gateway biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: gondoskodjon arról, hogy az összes Virtual Network Azure Application Gateway alhálózati üzemelő példány rendelkezzen egy hálózati biztonsági csoporttal (NSG), amely az alkalmazás megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel van alkalmazva. Habár a hálózati biztonsági csoportok támogatottak az Azure Application Gatewayon, vannak olyan korlátozások és követelmények, amelyeket be kell tartani ahhoz, hogy a NSG és az Azure-Application Gateway a várt módon működjön.

- [A korlátozások és követelmények ismertetése a NSG és az Azure Application Gateway használatával](configuration-overview.md)

- [Biztonsági konfigurációval rendelkező NSG létrehozása](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: az Azure Application Gateway alhálózatokhoz társított hálózati biztonsági csoportok (NSG-EK) esetében engedélyezze a NSG flow-naplókat, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

Megjegyzés: bizonyos esetekben előfordulhat, hogy az Azure Application Gateway alhálózatokhoz társított NSG-adatforgalmi naplók nem jelenítik meg az engedélyezett forgalmat. Ha a konfiguráció megfelel a következő forgatókönyvnek, nem jelenik meg az engedélyezett forgalom a NSG flow naplóiban:

- Telepítette Application Gateway v2-t
- Rendelkezik egy NSG az Application Gateway alhálózaton
- Engedélyezte a NSG folyamat naplóit az adott NSG

További információkért lásd az alábbi hivatkozásokat.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

- [Az Azure Application Gateway diagnosztikai és naplózási kérdései](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) üzembe helyezése a kritikus webalkalmazások előtt a bejövő forgalom további ellenőrzéséhez. A webalkalmazási tűzfal (WAF) az Azure Application Gateway szolgáltatása, amely központosított védelmet biztosít a webalkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen. Az Azure WAF segítségével biztonságossá teheti Azure App Service webalkalmazásait úgy, hogy a bejövő webes forgalmat úgy vizsgálja, hogy blokkolja a támadásokat, például az SQL-injektálásokat, a helyek közötti parancsfájlkezelést, a kártevők feltöltését és a DDoS-támadásokat. A WAF a OWASP (webalkalmazás-biztonsági projekt megnyitása) alapszabálya, a 3,1 (csak WAF_v2), a 3,0 és a 2.2.9. 

- [Az Azure Application Gateway funkcióinak megismerése](features.md)

- [Az Azure WAF ismertetése](../web-application-firewall/ag/ag-overview.md)

- [Az Azure WAF üzembe helyezése](../web-application-firewall/ag/create-waf-policy-ag.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a DDoS standard Protection engedélyezése az azure-Application Gateway üzemi példányaihoz társított Azure-beli virtuális hálózatokon a DDOS-támadások elleni védelem érdekében. Az ismert kártékony IP-címekkel folytatott kommunikáció megtagadásához használja Azure Security Center integrált veszélyforrások felderítését.

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: az Azure Application Gateway alhálózatokhoz társított hálózati biztonsági csoportok (NSG-EK) esetében engedélyezze a NSG flow-naplókat, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

Megjegyzés: bizonyos esetekben előfordulhat, hogy az Azure Application Gateway alhálózatokhoz társított NSG-adatforgalmi naplók nem jelenítik meg az engedélyezett forgalmat. Ha a konfiguráció megfelel a következő forgatókönyvnek, nem jelenik meg az engedélyezett forgalom a NSG flow naplóiban:

- Telepítette Application Gateway v2-t
- Rendelkezik egy NSG az Application Gateway alhálózaton
- Engedélyezte a NSG folyamat naplóit az adott NSG

További információkért lásd az alábbi hivatkozásokat.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

- [Az Azure Application Gateway diagnosztikai és naplózási kérdései](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) üzembe helyezése a kritikus webalkalmazások előtt a bejövő forgalom további ellenőrzéséhez. A webalkalmazási tűzfal (WAF) az Azure Application Gateway szolgáltatása, amely központosított védelmet biztosít a webalkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen. Az Azure WAF segítségével biztonságossá teheti Azure App Service webalkalmazásait úgy, hogy a bejövő webes forgalmat úgy vizsgálja, hogy blokkolja a támadásokat, például az SQL-injektálásokat, a helyek közötti parancsfájlkezelést, a kártevők feltöltését és a DDoS-támadásokat. A WAF a OWASP (webalkalmazás-biztonsági projekt megnyitása) alapszabálya, a 3,1 (csak WAF_v2), a 3,0 és a 2.2.9. 

Azt is megteheti, hogy több Piactéri lehetőség áll rendelkezésre, például az Azure-hoz készült Barracuda-WAF, amelyek az Azure piactéren érhetők el, beleértve az AZONOSÍTÓk/IP-

- [Az Azure Application Gateway funkcióinak megismerése](features.md)

- [Az Azure WAF ismertetése](../web-application-firewall/ag/ag-overview.md)

- [Az Azure WAF üzembe helyezése](../web-application-firewall/ag/create-waf-policy-ag.md)

- [A Barracuda WAF Cloud Service ismertetése](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: Azure-Application Gateway üzembe helyezése a megbízható tanúsítványokhoz engedélyezett https/SSL-alapú webalkalmazásokhoz.

- [Application Gateway üzembe helyezése](quick-create-portal.md)

- [A Application Gateway konfigurálása a HTTPS használatára](create-ssl-portal.md)

- [A 7. rétegbeli terheléselosztás és az Azure-webalkalmazás-átjárók ismertetése](overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: hálózati biztonsági csoportok vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához használjon Virtual Network szolgáltatás-címkéket. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például GatewayManager), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Az Azure Application Gateway alhálózatokhoz társított hálózati biztonsági csoportok (NSG-EK) esetében engedélyeznie kell a bejövő internetes forgalmat a 65503-65534-as TCP-portokon a Application Gateway v1 SKU esetében, valamint a v2 SKU-hoz készült 65200-65535-es TCP-portokat a GatewayManager szolgáltatás címkéje. Ez a porttartomány az Azure-infrastruktúra kommunikációja esetén szükséges. Ezeket a portokat az Azure-tanúsítványok védik (zárolják). A külső entitások, beleértve az átjárók ügyfeleit, nem tudnak kommunikálni ezeken a végpontokon.

- [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

- [Az Azure Application Gateway konfigurációjának áttekintése](configuration-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure Application Gateway üzemelő példányokhoz kapcsolódó hálózati beállítások szabványos biztonsági konfigurációinak meghatározása és implementálása. A "Microsoft. Network" névtérben Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure Application Gateway-, Azure-alapú virtuális hálózatok és hálózati biztonsági csoportok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Használhatja a beépített szabályzat-definíciót is.

Az Azure-tervrajzok segítségével leegyszerűsítheti a nagy léptékű Azure-környezeteket a főbb környezeti összetevők, például a Azure Resource Manager sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a házirendek egyetlen terv szerinti definícióban való kicsomagolásával. A tervet egyszerűen alkalmazhatja új előfizetésekre, környezetekre, valamint a verziószámozáson keresztül történő finomhangolásra és felügyeletre.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: az Azure Application Gateway alhálózatához társított hálózati biztonsági csoportok (NSG-EK), valamint a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrások használata címkék használatával. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy-definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a hálózati beállítások és az Azure Application Gateway üzemelő példányokhoz kapcsolódó erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati beállításokat vagy erőforrásokat módosítják.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a vezérlési sík naplózásához engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a naplókat log Analytics munkaterületre, Azure Event hub vagy Azure Storage-fiókba. Az Azure-tevékenység naplózási adatainak használatával meghatározhatja a "mi, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) az Azure-Application Gateway és a kapcsolódó erőforrások, például a hálózati biztonsági csoportok (NSG-EK) számára az Azure Application Gateway-alhálózat védelmére szolgáló műveletekhez.

A tevékenységek naplóin kívül az Azure Application Gateway üzemelő példányok diagnosztikai beállításait is konfigurálhatja. a diagnosztikai beállítások segítségével konfigurálhatja az erőforráshoz tartozó platform-naplók és-metrikák adatfolyam-exportálását a választott célra (Storage-fiókok, Event Hubs és Log Analytics).

Az Azure Application Gateway az Azure Application Insights beépített integrációját is biztosítja. Application Insights gyűjti a napló-, a teljesítmény-és a hiba adatait. Application Insights automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket, és hatékony elemzési eszközöket tartalmaz, amelyek segítenek a problémák diagnosztizálásában és a webalkalmazások használatának megismerésében. A folyamatos exportálást engedélyezheti a telemetria exportálásához a Application Insightsból egy központi helyre, hogy az adatok továbbra is a normál megőrzési időtartamnál hosszabbak maradjanak.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [Az Azure Application Gateway diagnosztikai beállításainak engedélyezése](application-gateway-diagnostics.md)

- [A Application Insights engedélyezése](../azure-monitor/app/app-insights-overview.md)

- [Folyamatos exportálás konfigurálása](../azure-monitor/app/export-telemetry.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a vezérlési sík naplózásához engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a naplókat log Analytics munkaterületre, Azure Event hub vagy Azure Storage-fiókba. Az Azure-tevékenység naplózási adatainak használatával meghatározhatja a "mi, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) az Azure-Application Gateway és a kapcsolódó erőforrások, például a hálózati biztonsági csoportok (NSG-EK) számára az Azure Application Gateway-alhálózat védelmére szolgáló műveletekhez.

A tevékenységek naplóin kívül az Azure Application Gateway üzemelő példányok diagnosztikai beállításait is konfigurálhatja. a diagnosztikai beállítások segítségével konfigurálhatja az erőforráshoz tartozó platform-naplók és-metrikák adatfolyam-exportálását a választott célra (Storage-fiókok, Event Hubs és Log Analytics).

Az Azure Application Gateway az Azure Application Insights beépített integrációját is biztosítja. Application Insights gyűjti a napló-, a teljesítmény-és a hiba adatait. Application Insights automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket, és hatékony elemzési eszközöket tartalmaz, amelyek segítenek a problémák diagnosztizálásában és a webalkalmazások használatának megismerésében. A folyamatos exportálást engedélyezheti a telemetria exportálásához a Application Insightsból egy központi helyre, hogy az adatok továbbra is a normál megőrzési időtartamnál hosszabbak maradjanak.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [Az Azure Application Gateway diagnosztikai beállításainak engedélyezése](application-gateway-diagnostics.md)

- [A Application Insights engedélyezése](../azure-monitor/app/app-insights-overview.md)

- [Folyamatos exportálás konfigurálása](../azure-monitor/app/export-telemetry.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure monitoron belül állítsa be a log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

- [Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az Azure-Tevékenységnaplók diagnosztikai beállításainak, valamint az azure-Application Gateway diagnosztikai beállításainak engedélyezése, valamint a naplók elküldése egy log Analytics-munkaterületre. Lekérdezéseket hajthat végre Log Analytics a kifejezések kereséséhez, a trendek azonosításához, a mintázatok elemzéséhez és számos más elemzéshez az összegyűjtött adatok alapján.

Az összes telepített hálózati erőforrás, beleértve az Azure Application Gateway-t, a hálózatokra vonatkozó Azure Monitor használatával átfogó képet kaphat az állapotról és a metrikáról. 

Opcionálisan engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [Az Azure Application Gateway diagnosztikai beállításainak engedélyezése](application-gateway-diagnostics.md)

- [Hálózati Azure Monitor használata](../azure-monitor/insights/network-insights-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: az Azure Web Application Firewall (WAF) v2 SKU üzembe helyezése kritikus webalkalmazások előtt a bejövő forgalom további ellenőrzéséhez. A webalkalmazási tűzfal (WAF) az Azure Application Gateway szolgáltatása, amely központosított védelmet biztosít a webalkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen. Az Azure WAF segítségével biztonságossá teheti Azure App Service webalkalmazásait úgy, hogy a bejövő webes forgalmat úgy vizsgálja, hogy blokkolja a támadásokat, például az SQL-injektálásokat, a helyek közötti parancsfájlkezelést, a kártevők feltöltését és a DDoS-támadásokat. A WAF a OWASP (webalkalmazás-biztonsági projekt megnyitása) alapszabálya, a 3,1 (csak WAF_v2), a 3,0 és a 2.2.9. 

Engedélyezze az Azure-műveletnapló diagnosztikai beállításait, valamint az Azure-WAF diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics munkaterületre. Lekérdezéseket hajthat végre Log Analytics a kifejezések kereséséhez, a trendek azonosításához, a mintázatok elemzéséhez és számos más elemzéshez az összegyűjtött adatok alapján.  A Log Analytics munkaterület-lekérdezések alapján létrehozhat riasztásokat.

Az összes telepített hálózati erőforrás, beleértve az Azure Application Gateway-t, a hálózatokra vonatkozó Azure Monitor használatával átfogó képet kaphat az állapotról és a metrikáról. A hálózati konzolon Azure Monitoron megtekintheti és létrehozhatja az Azure Application Gateway riasztásait.

- [Az Azure WAF üzembe helyezése](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [Az Azure Application Gateway diagnosztikai beállításainak engedélyezése](application-gateway-diagnostics.md)

- [Hálózati Azure Monitor használata](../azure-monitor/insights/network-insights-overview.md)

- [Riasztások létrehozása az Azure-ban](../azure-monitor/alerts/tutorial-response.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) v2 bevezetése a kritikus webalkalmazások előtt a bejövő forgalom további ellenőrzéséhez. A webalkalmazási tűzfal (WAF) az Azure Application Gateway szolgáltatása, amely központosított védelmet biztosít a webalkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen. Az Azure WAF segítségével biztonságossá teheti Azure App Service webalkalmazásait úgy, hogy a bejövő webes forgalmat úgy vizsgálja, hogy blokkolja a támadásokat, például az SQL-injektálásokat, a helyek közötti parancsfájlkezelést, a kártevők feltöltését és a DDoS-támadásokat.

Konfigurálja az Azure Application Gateway üzemelő példányok diagnosztikai beállításait. a diagnosztikai beállítások segítségével konfigurálhatja az erőforráshoz tartozó platform-naplók és-metrikák adatfolyam-exportálását a választott célra (Storage-fiókok, Event Hubs és Log Analytics).

- [Az Azure WAF üzembe helyezése](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Az Azure WAF diagnosztikai beállításainak konfigurálása](application-gateway-diagnostics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Active Directory (Azure ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: az Azure Application Gatewayhoz való hozzáférés szabályozása Azure Active Directory (Azure ad) segítségével történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:

- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

További információkért lásd az alábbi hivatkozásokat.

- [Az identitás és a hozzáférés figyelésének Azure Security Center használata (előzetes verzió)](../security-center/security-center-identity-access.md)

- [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: Azure-alkalmazás-regisztráció (egyszerű szolgáltatásnév) használata az Azure Application Gateway API-hívásokkal való interakcióhoz használható tokenek lekéréséhez.

- [Az Azure REST API-k meghívása](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Az ügyfélalkalmazás (egyszerű szolgáltatásnév) regisztrálása Azure Active Directory (Azure AD-vel)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Recovery Services API-információk](/rest/api/recoveryservices/)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: a mancsok (emelt szintű hozzáférésű munkaállomások) használata többtényezős hitelesítéssel, amely az Azure-erőforrások bejelentkezésére és konfigurálására van konfigurálva.

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory (Azure ad) biztonsági jelentések használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet Azure Active Directory (Azure ad) bejelentkezési tevékenységhez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely Siem/monitoring eszköz integrálását.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) Identity Protection és a kockázati észlelési funkciók használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését. 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Győződjön meg arról, hogy az összes Virtual Network Azure Application Gateway alhálózati üzemelő példány rendelkezik egy hálózati biztonsági csoporttal (NSG), amely az alkalmazás megbízható portjaira és forrásaira vonatkozó hálózati hozzáférés-vezérléssel van alkalmazva. Habár a hálózati biztonsági csoportok támogatottak az Azure Application Gatewayon, vannak olyan korlátozások és követelmények, amelyeket be kell tartani ahhoz, hogy a NSG és az Azure-Application Gateway a várt módon működjön.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [A korlátozások és követelmények ismertetése a NSG és az Azure Application Gateway használatával](configuration-overview.md)

- [Biztonsági konfigurációval rendelkező NSG létrehozása](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: gondoskodjon arról, hogy az összes Virtual Network Azure Application Gateway alhálózati üzemelő példány rendelkezzen egy hálózati biztonsági csoporttal (NSG), amely az alkalmazás megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel van alkalmazva. Korlátozza a kimenő forgalmat csak a megbízható helyek számára, hogy csökkentse az adatok kiszűrése veszélyét. Habár a hálózati biztonsági csoportok támogatottak az Azure Application Gatewayon, vannak olyan korlátozások és követelmények, amelyeket be kell tartani ahhoz, hogy a NSG és az Azure-Application Gateway a várt módon működjön.

- [A korlátozások és követelmények ismertetése a NSG és az Azure Application Gateway használatával](configuration-overview.md)

- [Biztonsági konfigurációval rendelkező NSG létrehozása](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: végpontok közötti titkosítás konfigurálása az Azure Application Gateway-átjárók TLS használatával.

- [Végpontok közötti TLS konfigurálása az Azure Application Gateway használatával](end-to-end-ssl-portal.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata az Azure Application Gateway vezérlési síkon való hozzáférés vezérléséhez (a Azure Portal).

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amelyekkel az Azure Application Gateway-példányok, valamint más kritikus vagy kapcsolódó erőforrások változásai zajlanak.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: jelenleg nem érhető el; a sebezhetőségi felmérés Azure Security Center az Azure Application Gateway számára még nem érhető el.

A Microsoft által beolvasott és javított mögöttes platform. Tekintse át az Azure Application Gateway számára elérhető biztonsági ellenőrzéseket a konfigurációval kapcsolatos biztonsági rések csökkentése érdekében.

- [A szolgáltatás lefedettsége (a sebezhetőségi felmérést is beleértve) az Azure Pásti szolgáltatásaihoz](../security-center/features-paas.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: még nem érhető el; a sebezhetőségi felmérés Azure Security Center az Azure Application Gateway számára még nem érhető el.

A Microsoft által beolvasott és javított mögöttes platform. Tekintse át az Azure Application Gateway számára elérhető biztonsági ellenőrzéseket a konfigurációval kapcsolatos biztonsági rések csökkentése érdekében.

- [A szolgáltatás lefedettsége (a sebezhetőségi felmérést is beleértve) az Azure Pásti szolgáltatásaihoz](../security-center/features-paas.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: még nem érhető el; a sebezhetőségi felmérés Azure Security Center az Azure Application Gateway számára még nem érhető el.

A Microsoft által beolvasott és javított mögöttes platform. Tekintse át az Azure Application Gateway számára elérhető biztonsági ellenőrzéseket a konfigurációval kapcsolatos biztonsági rések csökkentése érdekében.

- [A szolgáltatás lefedettsége (a sebezhetőségi felmérést is beleértve) az Azure Pásti szolgáltatásaihoz](../security-center/features-paas.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés (ok) n belül. Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: az Azure-erőforrások rendszerezéséhez és nyomon követéséhez használja a címkézést, a felügyeleti csoportokat és a különálló előfizetéseket, ahol szükséges. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

Emellett az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

További információkért lásd az alábbi hivatkozásokat.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetése (i) ban létrehozható erőforrások típusára. 

Az Azure Resource Graph segítségével lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait.  Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

További információkért lásd az alábbi hivatkozásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók "Microsoft Azure felügyelet" alkalmazáshoz való hozzáférésének tiltása a Azure Resource Manager való interakcióra.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Győződjön meg arról, hogy az összes Virtual Network Azure Application Gateway alhálózati üzemelő példány rendelkezik egy hálózati biztonsági csoporttal (NSG), amely az alkalmazás megbízható portjaira és forrásaira vonatkozó hálózati hozzáférés-vezérléssel van alkalmazva. Habár a hálózati biztonsági csoportok támogatottak az Azure Application Gatewayon, vannak olyan korlátozások és követelmények, amelyeket be kell tartani ahhoz, hogy a NSG és az Azure-Application Gateway a várt módon működjön.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [A korlátozások és követelmények ismertetése a NSG és az Azure Application Gateway használatával](configuration-overview.md)

- [Biztonsági konfigurációval rendelkező NSG létrehozása](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: az Azure Application Gateway üzemelő példányokhoz kapcsolódó hálózati beállítások szabványos biztonsági konfigurációinak meghatározása és implementálása. A "Microsoft. Network" névtérben Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure Application Gateway-, Azure-alapú virtuális hálózatok és hálózati biztonsági csoportok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Használhatja a beépített szabályzat-definíciót is.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure Policy [megtagadás] és a [telepítés, ha nem létezik] az Azure-erőforrások biztonságos beállításainak betartatására használható.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: Ha egyéni Azure-szabályzat-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Az Azure Repos dokumentációja](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: a "Microsoft. Network" névtérben található beépített Azure Policy definíciók és Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a "Microsoft. Network" névtérben található beépített Azure Policy definíciók és Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Az Azure Policy [audit], [megtagadás] és [telepítés ha nem létezik] használatával automatikusan kényszerítheti ki az Azure-erőforrások konfigurációit.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: felügyelt identitások használatával biztosíthatja az azure-Application Gateway automatikus felügyelt identitással Azure Active Directory (Azure ad) szolgáltatásban. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

A tanúsítványok biztonságos tárolásához használja a Azure Key Vault. A Azure Key Vault egy platform által felügyelt titkos tároló, amely a titkok, kulcsok és SSL-tanúsítványok védelmére használható. Az Azure Application Gateway támogatja a HTTPS-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványok Key Vault-integrációját. Ez a támogatás a Application Gateway v2 SKU-ra korlátozódik.

- [Az SSL-lezárás konfigurálása Key Vault tanúsítványokkal a Azure PowerShell használatával](configure-keyvault-ps.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használatával biztosíthatja az azure-Application Gateway automatikus felügyelt identitással Azure Active Directory (Azure ad) szolgáltatásban. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

A tanúsítványok biztonságos tárolásához használja a Azure Key Vault. A Azure Key Vault egy platform által felügyelt titkos tároló, amely a titkok, kulcsok és SSL-tanúsítványok védelmére használható. Az Azure Application Gateway támogatja a HTTPS-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványok Key Vault-integrációját. Ez a támogatás a Application Gateway v2 SKU-ra korlátozódik.

- [Az SSL-lezárás konfigurálása Key Vault tanúsítványokkal a Azure PowerShell használatával](configure-keyvault-ps.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) v2 bevezetése a kritikus webalkalmazások előtt a bejövő forgalom további ellenőrzéséhez. A webalkalmazási tűzfal (WAF) az Azure Application Gateway szolgáltatása, amely központosított védelmet biztosít a webalkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen. Az Azure WAF segítségével biztonságossá teheti Azure App Service webalkalmazásait úgy, hogy a bejövő webes forgalmat úgy vizsgálja, hogy blokkolja a támadásokat, például az SQL-injektálásokat, a helyek közötti parancsfájlkezelést, a kártevők feltöltését és a DDoS-támadásokat.

Konfigurálja az Azure Application Gateway üzemelő példányok diagnosztikai beállításait. a diagnosztikai beállítások segítségével konfigurálhatja az erőforráshoz tartozó platform-naplók és-metrikák adatfolyam-exportálását a választott célra (Storage-fiókok, Event Hubs és Log Analytics).

- [Az Azure WAF üzembe helyezése](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Az Azure WAF diagnosztikai beállításainak konfigurálása](application-gateway-diagnostics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) használata esetén WAF házirendeket konfigurálhat. A WAF-szabályzatok két különböző típusú biztonsági szabályt alkotnak: az ügyfél által létrehozott egyéni szabályokat, valamint a felügyelt szabálykészlet, amely az Azure által felügyelt, előre konfigurált szabályok gyűjteménye. Az Azure által felügyelt szabálykészlet egyszerű módszert kínál a védelem közös biztonsági fenyegetésekkel való üzembe helyezésére. Mivel az ilyen szabályrendszerek az Azure felügyeli, a szabályok az új támadási aláírások elleni védelemhez szükséges módon frissülnek.

- [Az Azure által felügyelt WAF-szabályok ismertetése](../web-application-firewall/ag/ag-overview.md#waf-policy-and-rules)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: az Azure Application Gateway nem tárolja az ügyféladatokat. Ha azonban egyéni Azure-szabályzat-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

Az Azure DevOps Services számos Azure Storage-funkciót használ, hogy gondoskodni tudjon az adatok rendelkezésre állásáról hardverhiba, szolgáltatáskimaradás vagy régiós katasztrófa esetén. Az Azure DevOps csapata olyan eljárásokat is követ, amelyek a véletlen vagy rosszindulatú törlésektől védi az adatokat.

- [Az adatelérhetőség ismertetése az Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Az Azure Repos dokumentációja](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: az ügyfél által felügyelt tanúsítványok biztonsági mentése Azure Key Vaulton belül.

- [Key Vault-tanúsítványok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az ügyfél által felügyelt tanúsítványok biztonsági másolatának visszaállítása.

- [Key Vault-tanúsítványok visszaállítása](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: Ellenőrizze, hogy engedélyezve van-e a soft delete Azure Key Vault. A Soft delete lehetővé teszi a törölt kulcstartók és tároló objektumok, például kulcsok, titkos kódok és tanúsítványok helyreállítását.

- [A Azure Key Vault Soft delete használata](../key-vault/general/key-vault-recovery.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint a riasztást eredményező tevékenység mögötti rosszindulatú szándékkal.

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
