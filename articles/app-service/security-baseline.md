---
title: App Service Azure biztonsági alapterve
description: A App Service biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7607b1726900423498a58502494c015f62da8829
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559434"
---
# <a name="azure-security-baseline-for-app-service"></a>App Service Azure biztonsági alapterve

Az App Service Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát. A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview-v1.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a app Service vonatkozó kapcsolódó útmutatás. A App Service nem alkalmazható **vezérlők** ki vannak zárva.

Ha szeretné megtekinteni, hogyan App Service teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes app Service biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

Útmutatás: Ha az elkülönített árképzési szinten App Servicet használ, más néven App Service Environment ( **bevezetési**), közvetlenül üzembe helyezhető az Azure-beli Virtual Networkban lévő alhálózatban. A hálózati biztonsági csoportokkal biztonságossá teheti a Azure App Service Environment a virtuális hálózatban lévő erőforrásokra irányuló bejövő és kimenő adatforgalom blokkolásával, vagy az alkalmazásokhoz való hozzáférés korlátozásával egy App Service Environment.

Alapértelmezés szerint a hálózati biztonsági csoportok tartalmaz egy implicit megtagadási szabályt a legalacsonyabb prioritással, és explicit engedélyezési szabályokat kell hozzáadnia. Adja hozzá a hálózati biztonsági csoport engedélyezési szabályait a legkevésbé privilegizált hálózati megközelítés alapján. A App Service Environment üzemeltetéséhez használt mögöttes virtuális gépek nem érhetők el közvetlenül, mert Microsoft által felügyelt előfizetésben vannak.

Az Azure-Application Gatewayt használó webalkalmazási tűzfalon (WAF) keresztüli útválasztási forgalom App Service Environment A szolgáltatás-végpontokat a Application Gateway együtt használva biztonságossá teheti a bejövő közzétételi forgalmat az alkalmazásba.  

A több-bérlős App Serviceban (egy nem elszigetelt szinten lévő alkalmazás) a hálózati biztonsági csoportok használatával tiltsa le az alkalmazásból érkező kimenő forgalmat. A Virtual Network integrációs funkciójával engedélyezheti az alkalmazások számára, hogy egy Virtual Networkon vagy azokon keresztül hozzáférjenek az erőforrásokhoz. Ezzel a szolgáltatással letilthatja a nyilvános címekre irányuló kimenő forgalmat az alkalmazásból. Virtual Network integráció nem használható az alkalmazáshoz való bejövő hozzáférés biztosításához.  

Biztonságos bejövő forgalom az alkalmazáshoz a következővel:
- Hozzáférési korlátozások – a bejövő hozzáférést vezérlő engedélyezési vagy megtagadási szabályok sorozata
- Szolgáltatási végpontok – megtagadhatja a bejövő forgalmat a megadott virtuális hálózatokon vagy alhálózatokon kívülről.
- Privát végpontok – az alkalmazás elérhetővé tétele a Virtual Network privát IP-címmel. Ha a privát végpontok engedélyezve vannak az alkalmazáson, az már nem érhető el az interneten

Ha Virtual Network integrációs funkciót használja ugyanabban a régióban lévő virtuális hálózatokkal, használja a hálózati biztonsági csoportokat és az útválasztási táblákat a felhasználó által megadott útvonalakkal. A felhasználó által megadott útvonalak az integrációs alhálózatra helyezhetők, hogy a kimenő forgalom a kívánt módon legyen elküldve.  

Az előfizetések és a virtuális hálózatok központi létrehozására, betartatására és naplózására az alkalmazás-és hálózati kapcsolatokra vonatkozó házirendeket központilag hozzon létre Azure Firewall. A Azure Firewall statikus nyilvános IP-címet használ a virtuális hálózati erőforrások számára, amely lehetővé teszi a külső tűzfalak számára a virtuális hálózatból származó forgalom azonosítását. 

- [App Service Environment zárolása](environment/firewall-integration.md)

- [A Web Application Security Project (OWASP) első 10 biztonsági rések elleni védelem megnyitása](https://owasp.org/www-project-top-ten/)

- [Hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md)

- [Alkalmazás integrálása egy Azure-beli virtuális hálózattal](web-sites-integrate-with-vnet.md)

- [App Service-környezet hálózati megfontolásai](environment/network-info.md)

- [Külső beszerzési útmutató létrehozása](environment/create-external-ase.md)

- [Belső beszerzési útmutató létrehozása](environment/create-ilb-ase.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

**Azure Policy beépített definíciók – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Security Center hálózati védelmi javaslatainak megvalósítása a app Service alkalmazásaihoz és API-khoz kapcsolódó hálózati erőforrások és konfigurációk biztonságossá tételéhez.

A Azure Firewall használatával elküldheti a forgalmat, és központilag hozhat létre, kényszerítheti és naplózhatja az alkalmazás-és hálózati csatlakozási házirendeket az előfizetések és a virtuális hálózatok Azure Firewall statikus nyilvános IP-címet használ a virtuális hálózati erőforrásaihoz, ami lehetővé teszi, hogy a tűzfalon kívüli forgalom azonosítsa a Virtual Networkból származó forgalmat. A Azure Firewall szolgáltatás teljes mértékben integrálva van Azure Monitor a naplózáshoz és az elemzéshez.

- [Azure Firewall áttekintése](../firewall/overview.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

- [A App Service monitorozásának és védelmének engedélyezése](../security-center/defender-for-app-service-introduction.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: az internetről elérhető alkalmazás biztonságossá tétele egy app Service Environmentban (bevezetési):
- Webalkalmazási tűzfal (WAF) üzembe helyezése az Azure Application Gateway egy internetre irányuló alkalmazás előtt
- Hozzáférési korlátozások használata a Application Gateway felé irányuló bejövő forgalom biztonságossá tételéhez
- Az alkalmazás biztonságossá tétele Azure Active Directory (Azure AD) használatával a hitelesítés biztosításához
- A TLS minimális verziójának beállítása 1,2-re
- Az alkalmazás beállítása csak HTTPS-re

Az összes alkalmazás kimenő forgalmát egy Azure Firewall eszközön keresztül hajtja végre, és figyeli a naplókat. 

Az internetről elérhető alkalmazások biztonságossá tétele a több-bérlős App Serviceban (például nem az elkülönített rétegben)
- Webalkalmazási tűzfalat támogató eszköz üzembe helyezése az alkalmazás előtt
- Hozzáférési korlátozások vagy szolgáltatási végpontok használata a webalkalmazási tűzfal (WAF) eszköz bejövő forgalmának biztonságossá tételéhez
- Az alkalmazás biztonságossá tétele az Azure AD-vel a hitelesítés biztosításához
- A TLS minimális verziójának beállítása 1,2-re
- Az alkalmazás beállítása csak HTTPS-re
- A Virtual Network Integration és az Alkalmazásbeállítások WEBSITE_VIRTUAL NETWORK_ROUTE_ALL, hogy az összes kimenő forgalom a hálózati biztonsági csoportoknak és a felhasználó által definiált útvonalakon is elérhető legyen az integrációs alhálózaton.

Az Application Service Environment-környezethez hasonlóan a Azure Firewall-eszközön keresztül minden alkalmazás-forgalom elküldése, valamint az alkalmazás naplófájljainak figyelése.

Emellett tekintse át és kövesse az App Service Environment dokumentumok zárolásával kapcsolatos javaslatokat.

- [App Service Environment zárolása](environment/firewall-integration.md)

- [Azure webalkalmazási tűzfal az Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Hozzáférési korlátozások Azure App Service](app-service-ip-restrictions.md)

- [WAF-riasztások nyomon követése és a trendek egyszerű figyelése Azure Monitor ](../azure-monitor/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a app Service Environment védelme a app Service Environment dokumentációjának zárolása című témakörben leírtak szerint. Alkalmazza az integrált veszélyforrások elleni intelligenciát a Azure Security Centerban, hogy megtagadja a kommunikációt az ismert kártevő vagy a nem használt nyilvános IP-címekkel. Hozzáférési korlátozások használata a Application Gateway felé irányuló bejövő forgalom biztonságossá tételéhez. 

Gondoskodjon a több-bérlős App Service (az alkalmazás nem elszigetelt szinten), egy nyilvános internetkapcsolattal rendelkező végponttal. Lehetővé teszi, hogy a forgalmat csak a Virtual Network egy adott alhálózatáról engedélyezze, és minden mást blokkol. A hozzáférési korlátozások használatával konfigurálja a hálózati Access Control-listát (IP-korlátozásokat) az engedélyezett bejövő forgalom zárolásához.

A rendezett engedélyezési vagy megtagadási lista prioritásának meghatározása az alkalmazáshoz való hálózati hozzáférés kezeléséhez. A lista tartalmazhat IP-címeket vagy Virtual Network alhálózatokat. Egy implicit "elutasítás all" szabály létezik a lista végén, ha egy vagy több bejegyzést tartalmaz. Ez a funkció minden App Service üzemeltetett munkaterheléssel működik, beleértve a következőket: Web Apps, API Apps, Linux-alkalmazások, Linux-tároló alkalmazások és függvények. 

A szolgáltatás-végpontok használatával korlátozhatja a webalkalmazáshoz való hozzáférést egy Azure-Virtual Network. Korlátozza a hozzáférést egy több-bérlős App Servicehoz (az alkalmazás nem elszigetelt szinten), a kiválasztott alhálózatokról a szolgáltatási végpontokkal. 

- [Az Azure App Service statikus IP-korlátozásai](app-service-ip-restrictions.md)

- [Azure webalkalmazási tűzfal az Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Webalkalmazási tűzfal (WAF) konfigurálása App Service Environmenthoz](environment/app-service-app-service-environment-web-application-firewall.md)

- [A következő témakörben leírtak szerint biztosítsa a beApp Service Environmentának zárolását:](environment/firewall-integration.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: a app Service-alkalmazások és a Security Center által küldött és onnan érkező kérelmek és válaszok figyelése. A webalkalmazások elleni támadásokat valós idejű Application Gateway felügyelheti, amely a webalkalmazási tűzfallal rendelkezik, és az integrált naplózási Azure Monitor segítségével nyomon követheti a webalkalmazási tűzfal riasztásait, és egyszerűen figyelheti a trendeket.

- [Azure webalkalmazási tűzfal az Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: alkalmazások forgalmának kezelése egy app Service Environmentban:

- A App Service Environment biztonságossá tétele a App Service Environment zárolásának letiltása című témakörben leírtak szerint
- Azure webalkalmazási tűzfallal rendelkező Application Gateway üzembe helyezése az internetre irányuló alkalmazások előtt
- Az alkalmazás beállítása, hogy csak HTTPS-kapcsolaton keresztül legyen elérhető

Az internetről elérhető alkalmazások forgalmának kezelése a több-bérlős App Serviceban (nem az elkülönített rétegben): 

- Azure webalkalmazási tűzfallal rendelkező Application Gateway üzembe helyezése az internetre irányuló alkalmazások előtt
- Hozzáférési korlátozások vagy szolgáltatási végpontok használata a webalkalmazási tűzfal felé irányuló bejövő forgalom biztonságossá tételéhez. A hozzáférési korlátozások funkció az összes App Service üzemeltetett munkaterheléssel működik, beleértve a Web Apps, a API Apps, a Linux-alkalmazások, a Linux-tároló alkalmazások és a függvények használatát.

- Az alkalmazás csak HTTPS-kapcsolaton keresztül érhető el
- Korlátozza a hozzáférést a App Service alkalmazáshoz statikus IP-korlátozásokkal, hogy az csak az alkalmazás-átjárón lévő VIP-forgalmat kapja meg, csak a hozzáféréssel rendelkező címet.

További információkért tekintse át a hivatkozott hivatkozásokat.

- [Az Azure App Service statikus IP-korlátozásai](app-service-ip-restrictions.md)

- [Azure webalkalmazási tűzfal az Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Végpontok közötti TLS konfigurálása Application Gateway használatával a portálon](../application-gateway/end-to-end-ssl-portal.md)

- [A következő témakörben leírtak szerint biztosítsa a beApp Serviceának zárolását:](/azure/app-service/environment/firewall-integration)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a app Service számos végpontot tartalmaz, amelyek a szolgáltatás felügyeletére szolgálnak. Ezeket a végponti címeket a AppServiceManagement IP-szolgáltatás címkéje is tartalmazza. A AppServiceManagement címkét csak egy App Service Environment használja az ilyen forgalom engedélyezéséhez. 

A megfelelő szolgáltatás forgalmának engedélyezéséhez vagy megtagadásához adja meg a szolgáltatási címke nevét a szabály megfelelő forrás vagy cél mezőjében. App Service a bejövő címeket a AppService IP-szolgáltatás címkéjén követjük nyomon. Nincs olyan IP-szolgáltatás címkéje, amely az App Service által használt kimenő címeket tartalmazza.

A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [Virtuális hálózati szolgáltatás címkéi](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása a app Service-alkalmazásokhoz kapcsolódó hálózati beállításokhoz. 

A biztonsági konfigurációk fenntartása Azure Policy aliasokkal a "Microsoft. Web" és a "Microsoft. Network" névterekben. Egyéni szabályzatokat hozhat létre a App Service-alkalmazások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. 

Beépített szabályzat-definíciókat használhat a App Servicehoz, például:
- Az alkalmazásnak virtuális hálózati szolgáltatási végpontot kell használnia
- Az alkalmazás csak HTTPS-kapcsolaton keresztül érhető el
- A minimális TLS-verzió beállítása a jelenlegi verzióra

További információkért tekintse át a hivatkozott hivatkozásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Végpontok közötti TLS konfigurálása Application Gateway használatával a portálon](../application-gateway/end-to-end-ssl-portal.md)

- [A következő témakörben leírtak szerint biztosítsa a beApp Serviceának zárolását:](/azure/app-service/environment/firewall-integration)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: használjon címkéket a hálózati biztonsági csoportokhoz és az egyéb kapcsolódó erőforrásokhoz, beleértve a app Service adatforgalmát.

Határozza meg az üzleti igényeket, az időtartamot és így tovább, az egyes szabályokhoz tartozó "Description" (Leírás) mezővel, amely lehetővé teszi, hogy az egyes hálózati biztonsági csoportok szabályai számára a hálózatról érkező vagy onnan érkező

Alkalmazza a címkézési hatásokkal kapcsolatos beépített Azure Policy definíciókat, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról. A Azure PowerShell vagy az Azure CLI használatával kereshet vagy végezhet műveleteket az erőforrásokon a címkék alapján.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Hozzáférési korlátozások Azure App Service](/azure/app-service/app-service-ip-restrictions)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrások konfigurációjának figyelésére, valamint a hálózati beállítások és a app Service kapcsolódó erőforrások változásainak észlelésére. 

Alkalmazza a App Service számos Azure Policy beépített definíciójának egyikét, például egy olyan szabályzatot, amely a virtuális hálózati végpont szolgáltatás használatára naplózza az alkalmazásokat. Riasztásokat hozhat létre Azure Monitoron belül, ha a kritikus hálózati beállítások vagy erőforrások módosításait szeretné elindítani. 

Tekintse át a részletes biztonsági riasztásokat és javaslatokat Security Center, a portálon vagy a programozott eszközökön. Exportálja ezt az információt, vagy küldje el más figyelési eszközökre a környezetben. Eszközök állnak rendelkezésre a riasztások és javaslatok manuális vagy folyamatos és folyamatos exportálásához. Ezekkel az eszközökkel a következőket teheti:
 
- Folyamatos exportálás Log Analytics munkaterületre
- Folyamatos Exportálás az Azure Event Hubsba (harmadik féltől származó SIEM-ekkel való integráció esetén)
- Exportálás CSV-fájlba (egy alkalommal)

Javasoljuk, hogy hozzon létre egy folyamatot automatizált eszközökkel a hálózati erőforrás-konfigurációk figyelésére és a változások gyors észlelésére.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

- [Biztonsági riasztások és javaslatok exportálása](../security-center/continuous-export.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

Útmutató: a App Service Environment ( **bevezetési**) integrálása Azure Monitorekkel az Azure Storage, az Azure Event Hubs vagy a log Analyticsba való küldéshez. Engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait a vezérlési sík naplózásához. Security Center biztonsági riasztásai közzé lesznek téve az Azure-tevékenység naplójában. Az Azure-beli tevékenységek naplózási adatok naplózása, amelyek lehetővé teszik, hogy meghatározza a "mi, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) a vezérlési sík szintjén a Azure App Service és más Azure-erőforrások esetében. Mentse a lekérdezéseket későbbi használatra, a lekérdezési eredményeket rögzítse az Azure-irányítópultokon, és hozzon létre napló-riasztásokat. Emellett a Application Insights adatelérési REST API is használhatja a telemetria programozott módon való eléréséhez.

Az üzleti igények alapján a különböző adatforrásokhoz és összekötőhöz való csatlakozáshoz használható Microsoft Azure Sentinel, skálázható, felhőalapú, biztonsági információkkal foglalkozó esemény-felügyeleti (SIEM) szolgáltatás. Emellett engedélyezheti és elvégezheti az adatok harmadik féltől származó biztonsági információkkal való ellátását (SIEM), például a Barracuda-t az Azure Marketplace-en.

- [Beléptetési tevékenység naplózása](./environment/using-an-ase.md#logging)

- [A Azure App Service diagnosztikai beállításainak engedélyezése](troubleshoot-diagnostic-logs.md)

- [A Application Insights engedélyezése](../azure-monitor/app/app-insights-overview.md)

- [Telemetria exportálása az Application Insightsból](../azure-monitor/app/export-telemetry.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: az Azure-Tevékenységnaplók diagnosztikai beállításainak engedélyezése a app Service vezérlési síkon történő naplózásához. Küldje el a naplókat egy Log Analytics munkaterületre, az Azure Event hub-ba vagy egy Azure Storage-fiókba.

A vezérlési sík szintjén végrehajtott összes írási művelethez (PUT, POST, DELETE) a "mit, ki és mikor" lehet meghatározni a App Service és az egyéb Azure-erőforrások Azure-beli tevékenység-naplózási adatai alapján.

Emellett a Azure Key Vault központosított titkos felügyeletet biztosít a hozzáférési házirendekkel és a naplózási előzményekkel. 

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [A Azure App Service diagnosztikai beállításainak engedélyezése](troubleshoot-diagnostic-logs.md)

- [Resource Manager-műveletek](../role-based-access-control/resource-provider-operations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitor a szervezet megfelelőségi előírásai szerint állítsa be a app Service erőforrásokhoz társított log Analytics munkaterületek naplózási megőrzési időtartamát.
- [Napló-megőrzési paraméterek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: Tekintse át az Azure-tevékenység naplójának diagnosztikai beállításait a app Service-erőforrásokban az log Analytics-munkaterületre küldött naplók használatával. Lekérdezéseket hajthat végre Log Analytics a kifejezések kereséséhez, a trendek azonosításához, a mintázatok elemzéséhez és számos más elemzéshez az összegyűjtött adatok alapján.

Használja Application Insights a App Service alkalmazásaihoz, valamint a napló-, a teljesítmény-és a hiba-adatok gyűjtéséhez. Megtekintheti Application Insights által gyűjtött telemetria-adatokat a Azure Portalon belül.

Ha telepített egy webalkalmazási tűzfalat (WAF), akkor a valós idejű webalkalmazási tűzfal naplójának használatával figyelheti a App Service-alkalmazások elleni támadásokat. A napló integrálva van Azure Monitor a webalkalmazási tűzfal értesítéseinek nyomon követésére és a trendek egyszerű figyelésére.

Az Azure Sentinel, a skálázható és a Felhőbeli natív biztonsági információkkal foglalkozó esemény kezelése (SIEM) a különböző adatforrásokkal és összekötővel való integrálásra igény szerint. Az Azure Marketplace-en engedélyezheti és elküldheti a harmadik féltől származó biztonsági információkkal kapcsolatos esemény-kezelési megoldást.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [A Application Insights engedélyezése](../azure-monitor/app/app-insights-overview.md)

- [A App Service Environment integrálása az Azure Application Gateway (WAF) használatával](environment/integrate-with-application-gateway.md)

- [A fedélzeti Azure Sentinel ismertetése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: konfigurálja Security Center az Azure-előfizetésében, és tekintse át a generált riasztásokat. A Azure Monitor használatával lekérheti a tevékenység naplójának adatait egy Event hub-ba, ahol egy biztonsági Information Event Management-(SIEM-) megoldás (például az Azure Sentinel) is olvasható. 

A valós idejű webalkalmazási tűzfal naplójának egy üzembe helyezett Azure webalkalmazási tűzfallal (WAF) való használatával figyelheti a App Service-alkalmazások elleni támadásokat. A napló integrálva van Azure Monitor a webalkalmazási tűzfal (WAF) riasztások nyomon követésére és a trendek egyszerű figyelésére.

- [A App Service Environment integrálása az Azure Application Gateway (WAF) használatával](environment/integrate-with-application-gateway.md)

- [Biztonsági riasztások és javaslatok exportálása](../security-center/continuous-export.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a Azure Active Directory (Azure ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni és lekérdezni. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Felügyelt identitások használata App Service és Azure Functions](./overview-managed-identity.md?tabs=dotnet&context=azure%2factive-directory%2fmanaged-identities-azure-resources%2fcontext%2fmsi-context)

- [Azure-szerepkörök kiosztása a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: Azure Active Directory (Azure ad) nem az alapértelmezett jelszavak fogalma. Vezérlési síkot biztosít a App Servicehoz való hozzáféréshez.

Általában Kerülje a felhasználói hozzáférés alapértelmezett jelszavainak megvalósítását a saját alkalmazások létrehozásakor. A App Service, például az Azure AD, a Microsoft-fiók, a Facebook, a Google vagy a Twitter számára alapértelmezés szerint elérhető identitás-szolgáltatók egyikét használja.

Tiltsa le a névtelen hozzáférést, kivéve, ha támogatni kell azt. 

- [A Azure App Serviceban alapértelmezés szerint elérhetők az identitás-szolgáltatók](./overview-authentication-authorization.md#identity-providers)

- [Hitelesítés és engedélyezés Azure App Service és Azure Functions](overview-authentication-authorization.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A Security Center identitás-és hozzáférés-kezelési funkciói segítségével figyelheti és nyomon követheti a rendszergazdai fiókok számát. 

Security Center vagy beépített Azure-szabályzatok javaslatainak használata, például:

- Az előfizetéshez több tulajdonost kell rendelni. 
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Hozzon létre egy folyamatot a hálózati erőforrás-konfigurációk figyelésére és a rendszergazdai fiókok változásainak észlelésére.

- [A Azure Security Center használata az identitás és a hozzáférés figyeléséhez](../security-center/security-center-identity-access.md)

- [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

- [További információ a felhasználók alkalmazásokhoz való hozzáférésének biztosításáról](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: app Service hitelesítése Azure Active Directory (Azure ad) használatával. OAuth 2,0 szolgáltatást biztosít az identitás-szolgáltató számára, és lehetővé teszi a mobil-és webalkalmazásokhoz való hozzáférési jogosultságot. 

App Service-alkalmazások összevont identitást használnak, amelyben a külső identitás-szolgáltató kezeli a felhasználói identitásokat és a hitelesítési folyamatot. Alapértelmezés szerint ezek az Identitáskezelő-szolgáltatók érhetők el:

- Azure AD
- Microsoft-fiók

- Facebook

- Google

- Twitter

Ha engedélyezi a hitelesítést és az engedélyezést ezen szolgáltatók egyikével, a bejelentkezési végpontja elérhetővé válik a felhasználói hitelesítéshez és a szolgáltatótól származó hitelesítési jogkivonatok érvényesítéséhez.

- [A hitelesítés és az engedélyezés ismertetése Azure App Service](./overview-authentication-authorization.md#identity-providers)

- [Tudnivalók a hitelesítésről és az engedélyezésről Azure App Service](overview-authentication-authorization.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze a többtényezős hitelesítés funkciót a Azure Active Directoryban (Azure ad), és kövesse az identitás-és hozzáférés-kezelési javaslatokat a Security Centerban.

Többtényezős hitelesítés implementálása az Azure AD-ben. A rendszergazdáknak biztosítaniuk kell, hogy a portálon lévő előfizetési fiókok védve legyenek. Az előfizetés sebezhető a támadásokkal szemben, mert kezeli a létrehozott erőforrásokat. 

- [Azure biztonsági többtényezős hitelesítés](/previous-versions/azure/security/develop/secure-aad-app)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: az Azure-erőforrások bejelentkezéséhez és konfigurálásához konfigurált, többtényezős hitelesítéssel rendelkező emelt szintű hozzáférésű munkaállomások (Paw) használata. 

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: a (z) Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenységek történnek.

Emellett az Azure AD kockázati észleléseit is használhatja a kockázatos felhasználói viselkedéssel kapcsolatos riasztások és jelentések megtekintéséhez.

A Security Center veszélyforrások elleni védelem átfogó védelmet biztosít a környezet számára, amely magában foglalja az Azure számítási erőforrások, például a Windows-gépek, a Linux-gépek, a App Service és az Azure-tárolók veszélyforrások elleni védelmét.

- [Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázati észlelések ismertetése](../active-directory/identity-protection/overview-identity-protection.md)

- [Veszélyforrások elleni védelem az Azure számítási erőforrásaihoz](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti a hozzáférést a Azure Portalhoz az IP-címtartományok, országok vagy régiók adott logikai csoportjaiból.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: az Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként a app Service alkalmazásokhoz. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz, valamint a felhasználók hitelesítő adatainak tárolásához és kiszűréséhez is.

- [A Azure App Service-alkalmazások konfigurálása az Azure AD-bejelentkezés használatára](configure-authentication-provider-aad.md)

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: az elavult fiókok felderítése Azure Active Directory (Azure ad) által biztosított naplók használatával. Az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok és a vállalati alkalmazásokhoz való hozzáférést, valamint a szerepkör-hozzárendeléseket. Rendszeresen tekintse át a felhasználók hozzáférését, és győződjön meg arról, hogy csak a kívánt felhasználók férhetnek hozzá. 

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: az Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként a app Service alkalmazásokhoz. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatok, a sók, a kivonatok és a biztonságos tárolók felhasználói hitelesítő adatai számára.

Az Azure AD bejelentkezési tevékenységekhez, naplózási és kockázati Eseménynapló-forrásokhoz való hozzáférés lehetővé teszi az Azure Sentinel vagy egy harmadik féltől származó biztonsági információ-kezelési (SIEM) megoldás integrálását. Az Azure AD-felhasználói fiókok diagnosztikai beállításainak létrehozásával és a naplózási és bejelentkezési naplók Log Analytics munkaterületre való elküldésével egyszerűsítheti a folyamatot. A kívánt naplózási riasztásokat Log Analytics belül lehet konfigurálni.

- [A Azure App Service-alkalmazások konfigurálása az Azure AD-bejelentkezés használatára](configure-authentication-provider-aad.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [A fedélzeti Azure Sentinel ismertetése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként a app Service alkalmazásokhoz. 

A Azure AD Identity Protection használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveleteket, például a fiók bejelentkezési viselkedését a vezérlési síkon a Azure Portal. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget. 

- [A Azure App Service alkalmazás konfigurálása az Azure AD-bejelentkezés használatára](configure-authentication-provider-aad.md)

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: nem érhető el; A Ügyfélszéf Azure App Service esetén nem támogatott.

- [Ügyfélszéf által támogatott szolgáltatások listája](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó app Service erőforrások nyomon követését.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: a app Service Environment külön előfizetéseket, felügyeleti csoportokat vagy mindkettőt implementálhat fejlesztési, tesztelési és éles környezetekben. Elkülönítheti azokat az alkalmazásokat, amelyek hasonló módon dolgozzák fel a bizalmas adatokat más alkalmazásokból. App Service alkalmazás üzembe helyezése egy Virtual Network. Használjon hálózati biztonsági csoportokat és alhálózatokat az alkalmazások további elkülönítéséhez. 

Két központi telepítési típus létezik egy App Service-környezethez. Mindkettő lehetővé teszi a forgalom elkülönítését az üzleti követelmények alapján.

- Külső alkalmazás-szolgáltatási környezet – a App Service Environment üzemeltetett alkalmazásokat elérhetővé teszi egy internetről elérhető IP-címen.

-  belső terheléselosztó (ILB) alkalmazás-szolgáltatási környezet – a Virtual Network belül elérhetővé teszi a App Service Environment üzemeltetett alkalmazásokat egy IP-címen. A belső végpont egy belső terheléselosztó (ILB), ezért ILB-előállítási módszernek nevezzük. 

A több-bérlős App Service (egy, az elkülönített rétegben nem szereplő alkalmazás) használja Virtual Network integrációt az alkalmazáshoz a virtuális hálózatban lévő erőforrásokhoz való hozzáféréshez.  A Private site Access használatával csak egy magánhálózaton, például egy Azure-beli virtuális hálózaton keresztül elérhetővé teheti az alkalmazást. Virtual Network integrációt csak az alkalmazásból kimenő hívások kezdeményezésére használja a rendszer a Virtual Network. A Virtual Network integrációs funkció másképp viselkedik, ha az azonos régióban található virtuális hálózattal és más régiókban található virtuális hálózatokkal van használatban. 
 
- [App Service-környezet hálózati megfontolásai](environment/network-info.md)

- [Külső beszerzési útmutató létrehozása](environment/create-external-ase.md)

- [Belső beszerzési útmutató létrehozása](environment/create-ilb-ase.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: bár az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem állnak rendelkezésre a app Service számára, a virtuális hálózatból csökkentheti az kiszűrése kockázatát azáltal, hogy eltávolítja az összes olyan szabályt, amelyben a cél az Internet vagy az Azure-szolgáltatások számára "címkét" használ. 

A Microsoft felügyeli a App Service mögöttes infrastruktúrát, és szigorú ellenőrzéseket vezetett be az adatvesztés vagy-expozíció megelőzése érdekében.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a TLS/SSL-beállításokban konfigurált TLS 1,2 alapértelmezett minimális verziójának használata az összes továbbítási adat titkosításához. Győződjön meg arról is, hogy az összes HTTP-kapcsolódási kérelem át lesz irányítva a HTTPS-re.

- [A Azure App Service webalkalmazások továbbításának ismertetése](security-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: jelenleg nem érhető el. Az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el App Service számára. 

Bizalmas adatokat feldolgozó alkalmazások címkézése App Service. A harmadik féltől származó megoldás implementálása, ha szükséges a megfelelőség szempontjából.

A Microsoft kezeli a mögöttes platformot, és az összes ügyféladatokat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú időt vesz igénybe. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC 

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata az Azure Active Directory (Azure ad-ban) a Azure Portal app Service vezérlési síkja elérésének vezérléséhez.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a webhely tartalmát egy app Service alkalmazásban, például a fájlokban tárolják az Azure Storage szolgáltatásban, amely automatikusan titkosítja a tartalmat a nyugalmi állapotban. Válassza az alkalmazás-titkok tárolása Key Vaultban lehetőséget, majd lekéri őket futásidőben.

Az ügyfél által megadott titkos kulcsok titkosítása App Service konfigurációs adatbázisokban történik.

Vegye figyelembe, hogy míg a helyileg csatlakoztatott lemezeket a webhelyek ideiglenes tárolóként (például D:\Local és% TMP%) is használhatják, nem titkosítva vannak.

- [Az Azure App Service adatvédelmes vezérlőinek ismertetése](./security-recommendations.md#data-protection)

- [Az Azure Storage titkosításának megismerése nyugalmi állapotban](../storage/common/storage-service-encryption.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure Activity log használatával riasztásokat hozhat létre az éles app Service alkalmazások és egyéb kritikus vagy kapcsolódó erőforrások változásairól.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: DevSecOps gyakorlat alkalmazása annak biztosítására, hogy a app Service alkalmazások biztonságosak legyenek, és az életciklusuk időtartama alatt biztosítva maradnak. A DevSecOps beépíti a szervezete biztonsági csapatát és képességeiket a DevOps-gyakorlatba, így biztonságot nyújtva mindenki számára a csapat számára.

Tekintse át és kövesse Security Center a App Service-alkalmazások biztonságossá tételéhez szükséges javaslatokat.

- [Folyamatos biztonsági ellenőrzés hozzáadása a CI/CD-folyamathoz](/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops&preserve-view=true)

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Microsoft a sebezhetőségek kezelését a app Service támogató mögöttes rendszereken hajtja végre. Ugyanakkor a Security Centeron belüli javaslatok súlyosságát, valamint a biztonságos pontszámot is használhatja a környezeten belüli kockázat méréséhez. A biztonságos pontszám azon alapul, hogy hány Security Center javasolt javaslat. Az első megoldáshoz szükséges javaslatok rangsorolása érdekében vegye figyelembe az egyes problémák súlyosságát.

- [Biztonsági javaslatok hivatkozási útmutatója](../security-center/recommendations-reference.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetéseken belüli összes erőforrást (például a számítási, tárolási, hálózati, portokat, protokollokat stb.). Győződjön meg arról, hogy a megfelelő engedélyek vannak alkalmazva a bérlőre, és az összes Azure-előfizetés, valamint az előfizetésekben található erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&preserve-view=true)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkék alkalmazása az Azure-erőforrásokra metaadatok használatával logikailag rendszerezheti őket egy besorolásban.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: az Azure-erőforrások rendszerezéséhez és nyomon követéséhez szükség szerint a címkézés, a felügyeleti csoportok és a különálló előfizetések használata. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a folyamat részeként a jogosulatlan erőforrások el legyenek távolítva az előfizetésből.

A következő beépített szabályzat-definíciók használatával megadhatja a korlátozásokat az ügyfél-előfizetésekben létrehozható erőforrások típusához: Azure Policy.

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

További információkért tekintse át a hivatkozott hivatkozásokat.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: jóváhagyott Azure-erőforrások és jóváhagyott szoftverek leltárának létrehozása a számítási erőforrások számára a szervezeti igények alapján.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetésekben létrehozható erőforrásokra vonatkozóan.

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésen belüli erőforrásokat.  Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetéseken belüli erőforrásokat, és biztosíthatja, hogy a felderített Azure-erőforrások jóváhagyása a szervezeti szabályzatok alapján történjen.

A App Service webjobs szolgáltatással figyelheti a számítási erőforrásokon belül üzembe helyezett nem jóváhagyott alkalmazásokat. A webjobs használatával futtathat egy programot vagy parancsfájlt ugyanabban a példányban, mint egy webalkalmazás, egy API-alkalmazás vagy egy mobil alkalmazás. Határozza meg a Webjobs konfigurációit és a figyelést a naplókkal. A Webjobs-Futtatás részletei lapon a kimenet váltása elemre kattintva jelenítse meg a napló tartalmának szövegét. Vegye figyelembe, hogy a webjobs-feladatok még nem támogatottak a Linux rendszeren való App Service.

- [Háttérben futó feladatok futtatása webjobs-feladatokkal Azure App Service](webjobs-create.md)

- [Oktatóanyag – szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Rövid útmutató – az első Resource Graph-lekérdezés futtatása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: gondoskodjon arról, hogy a környezetben lévő összes Azure-erőforrás jóvá legyen hagyva. A Azure Policy használatával korlátozásokat állíthat be az előfizetésekben létrehozható erőforrások típusára. Távolítsa el azokat a telepített szoftvereket, amelyek nem lettek jóváhagyva a szervezeti szabályzatok alapján.

- [Háttérben futó feladatok futtatása webjobs-feladatokkal Azure App Service](webjobs-create.md)

- [Oktatóanyag – szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Rövid útmutató – az első Resource Graph-lekérdezés futtatása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: gondoskodjon arról, hogy a környezetben lévő összes Azure-erőforrás jóvá legyen hagyva. A Azure Policy használatával korlátozásokat állíthat be az előfizetésekben létrehozható erőforrások típusára. Távolítsa el azokat a telepített szoftvereket, amelyek nem lettek jóváhagyva a szervezeti szabályzatok alapján. 

- [Háttérben futó feladatok futtatása webjobs-feladatokkal Azure App Service](webjobs-create.md)

- [Oktatóanyag – szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Rövid útmutató – az első Resource Graph-lekérdezés futtatása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: hozzon létre egy folyamatot, amely rendszeres időközönként áttekinti a jogosulatlan Azure-szolgáltatásokat, hogy csak az engedélyezett Azure-szolgáltatások legyenek használatban az előfizetésekben.

Az Azure Resource Graph ezen a folyamaton belül az előfizetésen belüli erőforrások lekérdezésére vagy felderítésére használható. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

A következő beépített szabályzat-definíciók segítségével konfigurálja a Azure Policy az előfizetésekben létrehozható erőforrások típusának korlátozására:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

A App Service webjobs szolgáltatással figyelheti a nem jóváhagyott szoftveralkalmazások számítógép-erőforrásokon belüli telepítését. A webjobs használatával futtathat egy programot vagy parancsfájlt ugyanabban a példányban, mint egy webalkalmazás, egy API-alkalmazás vagy egy mobil alkalmazás. Határozza meg a Webjobs konfigurációit és a figyelést a naplókkal. A Webjobs-Futtatás részletei lapon a kimenet váltása elemre kattintva jelenítse meg a napló tartalmának szövegét. Vegye figyelembe, hogy a webjobs-feladatok még nem támogatottak a Linux rendszeren való App Service.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Háttérben futó feladatok futtatása webjobs-feladatokkal Azure App Service](webjobs-create.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: az előfizetésekben lévő szoftverek címeinek leltározására és felülvizsgálatára szolgáló folyamat megvalósítása rendszeres rendszerességgel, hogy csak a jogosult Azure-szolgáltatások legyenek felhasználva az előfizetésekben.

Az Azure Resource Graph ezen a folyamaton belül az előfizetéseken belüli erőforrások lekérdezésére vagy felderítésére használható. Győződjön meg arról, hogy a környezetben felderített összes Azure-erőforrás jóvá van hagyva.

Konfigurálja a Azure Policyt az ügyfél-előfizetésekben létrehozható erőforrások típusának korlátozására a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Ehhez hasonlóan a App Service webjobs szolgáltatásban is használhatja a nem jóváhagyott, számítógép-erőforrásokon belül telepített szoftveralkalmazások leltározását. Határozza meg a konfigurációt és a figyelést a naplókkal. A Webjobs-Futtatás részletei lapon a kimenet váltása elemre kattintva jelenítse meg a napló tartalmának szövegét. Vegye figyelembe, hogy a webjobs-feladatok még nem támogatottak a Linux rendszeren való App Service.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók a Azure Resource Manager való interakciójának korlátozására a "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: a app Service webjobs szolgáltatás lehetővé teszi, hogy az ügyfelek egy olyan programot vagy parancsfájlt futtassanak ugyanabban a példányban, mint egy webalkalmazás, egy API-alkalmazás vagy egy mobil alkalmazás. Ön felelős azért, hogy meghatározza a konfigurációt, hogy korlátozza vagy korlátozza az összes olyan parancsfájlt, amelyet a szervezet nem engedélyez. A App Service nem biztosít olyan mechanizmust, amellyel a parancsfájlok végrehajtása natív módon korlátozható. Vegye figyelembe, hogy a webjobs-feladatok még nem támogatottak a Linux rendszeren való App Service.

- [Háttérben futó feladatok futtatása webjobs-feladatokkal Azure App Service](webjobs-create.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: különálló előfizetések vagy felügyeleti csoportok implementálása a nagy kockázatú app Service alkalmazások elkülönítésének biztosításához. Magasabb kockázatú alkalmazás üzembe helyezése a saját Virtual Network, mivel a App Service peremhálózati biztonsági funkciói a virtuális hálózatok használatával valósulnak meg. A App Service Environment a App Service üzembe helyezése az Azure-Virtual Network egy alhálózatán. 

Az Application Service-környezetnek két típusa van, a külső Application Service-környezet és a ILB (belső Load Balancer) Application Service-környezet. A követelmények alapján válassza ki a legjobb architektúrát.

- [App Service-környezet hálózati megfontolásai](environment/network-info.md)

- [Külső App Service-környezet létrehozása](environment/create-external-ase.md)

- [Belső Load Balancer létrehozása és használata App Service Environment](environment/create-ilb-ase.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása a app Service telepített alkalmazásokhoz Azure Policy használatával.

A "Microsoft. Web" névtérben Azure Policy Aliasok használatával egyéni házirendeket hozhat létre a App Service Web Apps konfigurációjának naplózásához vagy érvényesítéséhez.

Beépített szabályzat-definíciók alkalmazása, például:

- App Service virtuális hálózati szolgáltatás végpontját kell használnia

- A webalkalmazások csak HTTPS protokollon keresztül érhetők el

- A legújabb TLS-verzió használata az alkalmazásokban

Azt javasoljuk, hogy a beépített szabályzat-definíciók alkalmazásával dokumentálja a szabványos használatot.   

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&preserve-view=true)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: a Azure Policy [megtagadás] és a [telepítés, ha nem létezik] hatással van a Azure app Service alkalmazások biztonságos beállításainak betartatására.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: válassza az Azure DevOps vagy az Azure Repos lehetőséget a kód biztonságos tárolásához és kezeléséhez az egyéni Azure Policy-definíciók használatakor.

A meglévő folyamatos integrációs (CI) és folyamatos továbbítási (CD) folyamat használatával egy ismert, biztonságos konfigurációt telepíthet.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

- [Az Azure Repos dokumentációja](/azure/devops/repos/?view=azure-devops&preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: beépített Azure Policy definíciók és Azure Policy aliasok használata a "Microsoft. Web" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Dolgozzon ki egy folyamatot és folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: beépített Azure Policy definíciók és Azure Policy aliasok használata a "Microsoft. Web" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. 

Alkalmazza a Azure Policy [audit], [megtagadás] és [üzembe helyezés, ha nem létezik], az effektusok az Azure-erőforrások konfigurációinak automatikus érvényesítéséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: felügyelt identitások használatával biztosíthatja a app Service-alkalmazások automatikus felügyelt identitását a Azure Active Directoryban (Azure ad). A felügyelt identitások lehetővé teszik az alkalmazások számára az Azure AD-hitelesítést támogató bármely szolgáltatás hitelesítését, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül. Győződjön meg arról, hogy a Soft delete engedélyezve van a Azure Key Vaultban.

- [A Soft delete engedélyezése Azure Key Vault](../key-vault/general/key-vault-recovery.md)

- [Felügyelt identitások használata App Servicehoz](overview-managed-identity.md)

- [Key Vault hitelesítés biztosítása felügyelt identitással](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használatával biztosíthatja a app Service által telepített alkalmazásokat automatikusan felügyelt identitással a Azure Active Directoryban (Azure ad). A felügyelt identitások lehetővé teszik az alkalmazások számára az Azure AD-hitelesítést támogató bármely szolgáltatás hitelesítését, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Felügyelt identitások használata App Servicehoz](overview-managed-identity.md)

- [Key Vault hitelesítés biztosítása felügyelt identitással](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a app Service biztonsági mentési és visszaállítási funkciója lehetővé teszi, hogy manuálisan vagy ütemezés szerint egyszerűen hozza létre az alkalmazások biztonsági másolatait. Beállíthatja, hogy a biztonsági másolatok határozatlan ideig maradjanak. Az alkalmazást visszaállíthatja egy korábbi állapot pillanatképére a meglévő alkalmazás felülírásával vagy egy másik alkalmazásra való visszaállítással.

App Service a következő információk biztonsági mentését végezheti el egy Azure Storage-fiókba és-tárolóba, amelyet az alkalmazás használatára konfigurált:
- Alkalmazáskonfiguráció
- Fájl tartalma
- Az alkalmazáshoz csatlakoztatott adatbázis

Győződjön meg arról, hogy a rendszeres és automatizált biztonsági másolatok a szervezeti szabályzatok által meghatározott gyakorisággal történnek.

- [Azure App Service biztonsági mentési képesség ismertetése](manage-backup.md)

- [Ügyfél által felügyelt kulcsok az Azure Storage-titkosításhoz](../storage/common/customer-managed-keys-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a app Service biztonsági mentési és visszaállítási funkciójának használata az alkalmazások biztonsági mentéséhez. A biztonsági mentési funkciókhoz Azure Storage-fiók szükséges az alkalmazás biztonsági mentési adatainak tárolásához.

- Az Azure Storage biztosítja a titkosítást a nyugalmi állapotban – a rendszerek által biztosított kulcsokkal vagy a saját, az ügyfél által felügyelt kulcsokkal. Itt tárolódnak az alkalmazásadatok, ha az nem egy Azure-beli webalkalmazásban fut.
- A központi telepítési csomagból való futtatás a App Service központi telepítési funkciója. Lehetővé teszi a webhely tartalmának Azure Storage-fiókból való üzembe helyezését egy közös hozzáférési aláírás (SAS) URL-cím használatával.

- Key Vault referenciák App Service biztonsági funkciója. Lehetővé teszi, hogy az alkalmazás beállításainak megfelelően importálja a titkokat futásidőben. Ezzel titkosíthatja az Azure Storage-fiók SAS URL-címét.

További információ a hivatkozott hivatkozásokon érhető el.

- [Adatok biztonsági mentése az Azure-ban](manage-backup.md)

- [Azure App Service-ben futó alkalmazás visszaállítása](web-sites-restore.md)

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Titkosítási modell és kulcskezelő tábla](../security/fundamentals/encryption-atrest.md)

- [Inaktív adatok titkosítása az ügyfél által felügyelt kulcsok használatával](configure-encrypt-at-rest-using-cmk.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: rendszeres időközönként tesztelje a app Service-alkalmazások biztonsági mentésének visszaállítási folyamatát.

- [Adatok biztonsági mentése az Azure-ban](manage-backup.md)

- [Azure App Service webalkalmazás visszaállítása](web-sites-restore.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: app Service biztonsági mentések egy Azure Storage-fiókban tárolódnak. Az Azure Storage-ban tárolt adatai a 256 bites AES-titkosítással vannak titkosítva és visszafejtve, és az egyik legerősebb blokk titkosítási algoritmus, amely az FIPS 140-2-kompatibilis. Az Azure Storage-titkosítás hasonló a Windows rendszeren a BitLocker-titkosításhoz.

Az Azure Storage-titkosítás minden Storage-fiók esetében engedélyezve van, beleértve a Resource Manager és a klasszikus Storage-fiókokat is. Az Azure Storage-titkosítás nem tiltható le. Mivel az adatai alapértelmezés szerint biztonságosak, nem kell módosítania a kódot vagy az alkalmazásokat, hogy kihasználhassa az Azure Storage-titkosítás előnyeit.

Alapértelmezés szerint a Storage-fiókokban lévő adatforgalom a Microsoft által felügyelt kulcsokkal van titkosítva. Hivatkozhat a Microsoft által felügyelt kulcsokra az adatok titkosításához, vagy kezelheti a titkosítást a saját kulcsaival. Győződjön meg arról, hogy a Soft delete engedélyezve van a Azure Key Vaultban.

- [Az Azure Storage-titkosítás megismerése a REST-adatok esetében](../storage/common/storage-service-encryption.md)

- [A Soft delete engedélyezése Azure Key Vault](../key-vault/general/key-vault-recovery.md)

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

Emellett egyértelműen megjelölheti az előfizetéseket (például éles környezetben, nem éles környezetben), és létrehozhat egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: gyakorlatok végrehajtása a rendszer incidens-reagálási képességeinek teszteléséhez egy normál ütemben. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát, amely az IT-csomagokhoz és-képességekhez kapcsolódó programok tesztelését, betanítását és gyakorlatát ismerteti](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte.  A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Security Center adatösszekötővel üzleti igényeknek megfelelően továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

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
