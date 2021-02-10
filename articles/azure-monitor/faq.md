---
title: Azure Monitor GYIK | Microsoft Docs
description: Válaszok a Azure Monitorekkel kapcsolatos gyakori kérdésekre.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2020
ms.openlocfilehash: a8d3ded1d11a350ff53ffda71348b2cc707760b8
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008417"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure Monitor gyakori kérdések

A Microsoft gyakori kérdései a Azure Monitorkal kapcsolatos gyakori kérdések listája. Ha további kérdése van, látogasson el a [vitafórumra](/answers/questions/topics/single/24223.html) , és tegye fel kérdéseit. Ha egy kérdést gyakran megkérdeznek, azt a cikkhez adja hozzá, hogy gyorsan és könnyen elérhető legyen.


## <a name="general"></a>Általános

### <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?
[Azure monitor](overview.md) egy Azure-szolgáltatás, amely teljesítmény-és rendelkezésre állási monitorozást biztosít az Azure-ban, más felhőalapú környezetekben vagy helyszíni környezetben futó alkalmazások és szolgáltatások számára. Azure Monitor a különböző forrásokból származó adatokat egy közös adatplatformba gyűjti, ahol elemezni lehet a trendeket és a rendellenességeket. A Azure Monitor gazdag funkciói segítenek gyorsan azonosítani és reagálni azokra a kritikus helyzetekre, amelyek hatással lehetnek az alkalmazásra.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Mi a különbség a Azure Monitor, a Log Analytics és a Application Insights között?
Szeptember 2018-án a Microsoft kombinált Azure Monitor, Log Analytics és Application Insights egyetlen szolgáltatásba, amely hatékony, teljes körű monitorozást tesz lehetővé az alkalmazások és a rájuk támaszkodó összetevők számára. A Log Analytics és Application Insights funkciói nem változtak, bár egyes funkciók a Azure Monitorre lettek állítva, hogy jobban tükrözzék az új hatókört. A Log Analytics naplózási adatmotorja és lekérdezési nyelve már Azure Monitor naplók néven is ismert. Lásd: [Azure monitor terminológiai frissítések](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Mit jelent a Azure Monitor díja?
A Azure Monitor automatikusan engedélyezett szolgáltatásai, például a metrikák és a tevékenységek naplóinak gyűjtése díjmentes. Más szolgáltatásokkal, például a naplók lekérdezésével és a riasztásokkal kapcsolatos költségeket is tartalmaz. A részletes díjszabási információkért tekintse meg a [Azure monitor díjszabási oldalát](https://azure.microsoft.com/pricing/details/monitor/) .

### <a name="how-do-i-enable-azure-monitor"></a>Hogyan az Azure Monitor engedélyezése?
Azure Monitor engedélyezve van az új Azure-előfizetés létrehozásakor, valamint a [tevékenység naplójának](./platform/platform-logs-overview.md) és a platform [metrikáinak](platform/data-platform-metrics.md) automatikus gyűjtése. [Diagnosztikai beállításokat](platform/diagnostic-settings.md) hozhat létre az Azure-erőforrások működésével kapcsolatos részletesebb információk gyűjtéséhez, valamint a [figyelési megoldások](insights/solutions.md) és elemzések hozzáadásához [, hogy](./monitor-reference.md) további elemzéseket nyújtson az egyes szolgáltatások összegyűjtött adatairól. 

### <a name="how-do-i-access-azure-monitor"></a>Hogyan a hozzáférési Azure Monitor?
A Azure Portal **figyelés** menüjében található összes Azure monitor funkció és az adatok elérése. A különböző Azure-szolgáltatások menüjének **figyelés** szakasza ugyanazokat az eszközöket biztosítja, mint az adott erőforráshoz szűrt adatok. Azure Monitor adatok a CLI, a PowerShell és a REST API használatával számos különböző forgatókönyvhöz is elérhetők.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Van Azure Monitor helyszíni verziója?
Nem. A Azure Monitor egy skálázható felhőalapú szolgáltatás, amely nagy mennyiségű adattal dolgoz fel és tárol, bár a Azure Monitor a helyszínen és más felhőkben lévő erőforrásokat is képes figyelni.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Képes Azure Monitor figyelni a helyszíni erőforrásokat?
Igen, az Azure-erőforrásokból származó monitorozási adatok gyűjtése mellett Azure Monitor adatokat gyűjthet a virtuális gépekről és az egyéb felhőben és a helyszínen lévő alkalmazásokból is. Tekintse [meg Azure monitor figyelési adatforrásait](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Azure Monitor integrálódik a System Center Operations Manager?
A meglévő System Center Operations Manager felügyeleti csoport összekapcsolásával Azure Monitorheti az ügynököktől származó adatok Azure Monitor naplókba való gyűjtését. Ez lehetővé teszi a naplók és a megoldás használatát az ügynököktől gyűjtött adatok elemzéséhez. A meglévő System Center Operations Manager ügynököket úgy is konfigurálhatja, hogy közvetlenül a Azure Monitor küldje el az adatküldést. Lásd: [Operations Manager kapcsolódása Azure monitorhoz](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Milyen IP-címeket Azure Monitor használni?
Tekintse meg az [Application Insights által használt IP-címeket, és log Analytics](app/ip-addresses.md) az ügynökökhöz és más külső erőforrásokhoz szükséges IP-címek és portok listáját a Azure monitor eléréséhez. 

## <a name="monitoring-data"></a>Adatok monitorozása

### <a name="where-does-azure-monitor-get-its-data"></a>Hol Azure Monitor lekérni az adatgyűjtést?
A Azure Monitor különböző forrásokból gyűjt adatokat, beleértve az Azure platformról és erőforrásokból, az egyéni alkalmazásokból és a virtuális gépeken futó ügynököktől származó naplókat és mérőszámokat. Más szolgáltatások, például a Azure Security Center és a Network Watcher adatokat gyűjtenek egy Log Analytics munkaterületen, így Azure Monitor adatokkal elemezhetők. A naplók és a metrikák REST API használatával egyéni adatokat is küldhet Azure Monitor. Tekintse [meg Azure monitor figyelési adatforrásait](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Milyen adatokat gyűjtenek a Azure Monitor? 
Azure Monitor a különböző forrásokból származó adatokat [naplókba](platform/data-platform-logs.md) vagy [metrikába](platform/data-platform-metrics.md)gyűjti. Az egyes adattípusok viszonylagos előnyökkel rendelkeznek, és mindegyikük a Azure Monitor szolgáltatásainak egy adott készletét támogatja. Minden Azure-előfizetéshez egyetlen metrikai adatbázis van, míg a követelményektől függően több Log Analytics munkaterületet is létrehozhat a naplók gyűjtéséhez. Lásd: [Azure monitor adatplatform](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Van-e a Azure Monitor összegyűjtött adatok maximális mennyisége?
A begyűjthető metrikus adatok mennyisége nincs korlátozva, de ezeket az adatokat legfeljebb 93 napig tároljuk. Lásd: [mérőszámok megőrzése](platform/data-platform-metrics.md#retention-of-metrics). Az összegyűjtött naplózási adatok mennyisége nem korlátozható, de a Log Analytics munkaterülethez választott díjszabási szinten is hatással lehet. Tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Hogyan Azure Monitor által gyűjtött adatokhoz való hozzáférést?
Az elemzések és megoldások egyéni felhasználói élményt biztosítanak a Azure Monitor tárolt adatkezelési feladatok elvégzéséhez. A Kusto lekérdezési nyelvben (KQL) írt napló lekérdezéssel közvetlenül is dolgozhat a naplózási adataival. A Azure Portalban lekérdezéseket írhat és futtathat, és interaktív módon elemezheti az adatelemzést Log Analytics használatával. A Azure Portal metrikáinak elemzése a Metrikaböngésző. Lásd: a [naplózási információk elemzése Azure monitor](log-query/log-query-overview.md) és [Az Azure Metrikaböngésző első lépései](platform/metrics-getting-started.md).

## <a name="solutions-and-insights"></a>Megoldások és bepillantást nyerhet

### <a name="what-is-an-insight-in-azure-monitor"></a>Mi a Azure Monitor betekintése?
Az adatok testreszabott figyelési élményt nyújtanak az adott Azure-szolgáltatásokhoz. Ugyanazokat a metrikákat és naplókat használják, mint a Azure Monitor egyéb funkciói, de további adatokat gyűjthetnek, és egyedi felhasználói élményt nyújthatnak a Azure Portal. Tekintse [meg Azure monitor](./monitor-reference.md).

A Azure Portalban megjelenő információk megtekintéséhez tekintse meg **a szolgáltatás** menüjének **figyelés** menüjének és **figyelés** szakaszának áttekintés szakaszát.

### <a name="what-is-a-solution-in-azure-monitor"></a>Mi az a megoldás a Azure Monitor?
A figyelési megoldások egy adott alkalmazás vagy szolgáltatás Azure Monitor szolgáltatások alapján történő figyelésére szolgáló, csomagolt logikai készletek. A naplózási adatokat Azure Monitorba gyűjtik, és a Azure Portal gyakori felhasználói felületén keresztül nyújtanak naplózási lekérdezéseket és nézeteket az elemzéshez. Lásd: [Azure monitor figyelési megoldásai](insights/solutions.md).

Ha meg szeretné tekinteni a megoldásokat a Azure Portalban, kattintson **a** **figyelés** menü áttekintések szakaszában található **továbbiak** elemre. Kattintson a **Hozzáadás** gombra további megoldások hozzáadásához a munkaterülethez.

## <a name="logs"></a>Naplók

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Mi a különbség a Azure Monitor naplók és az Azure Adatkezelő között?
Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Azure Monitor naplók az Azure Adatkezelőra épülnek, és ugyanazokat a Kusto-lekérdezési nyelvet (KQL) használják néhány kisebb eltéréssel. Lásd: [Azure monitor a naplózási lekérdezés nyelvi különbségeit](/azure/data-explorer/kusto/query/).

### <a name="how-do-i-retrieve-log-data"></a>Hogyan beolvasni az adatnaplót?
Az összes adatok beolvasása egy Log Analytics munkaterületről a Kusto Query Language (KQL) használatával írt napló lekérdezés használatával. Írhat saját lekérdezéseket, vagy használhat olyan megoldásokat és bepillantást, amelyek egy adott alkalmazáshoz vagy szolgáltatáshoz tartozó naplózási lekérdezéseket tartalmaznak. Lásd: [Azure monitorban található naplók áttekintése](log-query/log-query-overview.md).

### <a name="can-i-delete-data-from-a-log-analytics-workspace"></a>Törölhetek adatok Log Analytics munkaterületről?
Az adatok el lettek távolítva a munkaterületről a [megőrzési időtartamnak](platform/manage-cost-storage.md#change-the-data-retention-period)megfelelően. A megadott adatokat adatvédelmi vagy megfelelőségi okokból is törölheti. További információkért lásd: [privát adatok exportálása és törlése](platform/personal-data-mgmt.md#how-to-export-and-delete-private-data) .

### <a name="is-log-analytics-storage-immutable"></a>Nem változtathatók Log Analytics a tárolók?
Az adatbázis-tárolóban tárolt adatmennyiség nem módosítható a betöltés után, de törölhető [a *kiürítési* API-útvonalon keresztül a magánjellegű adattörléshez](platform/personal-data-mgmt.md#delete). Bár az adat nem módosítható, bizonyos minősítésekhez szükséges, hogy az adat nem módosítható és nem törölhető a tárolóban. Az adatmódosíthatatlansági a nem módosítható [tárolóként](../storage/blobs/storage-blob-immutability-policies-manage.md)konfigurált Storage-fiókba történő [adatexportálással](platform/logs-data-export.md) érhető el.

### <a name="what-is-a-log-analytics-workspace"></a>Mi a Log Analytics-munkaterület?
A Azure Monitor által gyűjtött összes naplózási adatokat egy Log Analytics munkaterületen tárolja a rendszer. A munkaterület lényegében egy olyan tároló, amelyben a naplózási adatokat különböző forrásokból gyűjti a rendszer. Lehet, hogy az összes figyelési adathoz egyetlen Log Analytics munkaterület tartozik, vagy több munkaterületre vonatkozó követelmények is lehetnek. Lásd: [a Azure monitor naplók üzembe helyezésének megtervezése](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Át lehet helyezni egy meglévő Log Analytics munkaterületet egy másik Azure-előfizetésbe?
Áthelyezheti a munkaterületet erőforráscsoportok vagy előfizetések között, de nem egy másik régióba. Lásd: [log Analytics munkaterület áthelyezése másik előfizetésre vagy erőforráscsoporthoz](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Miért nem látom a Query Explorer és a Save gombokat a Log Analyticsban?

A **lekérdezési tallózó**, a **Mentés** és az **új riasztási szabály** gomb nem érhető el, ha a [lekérdezési hatókör](log-query/scope.md) egy adott erőforrásra van beállítva. Riasztások létrehozásához, illetve egy lekérdezés mentéséhez vagy betöltéséhez Log Analytics hatókörét egy munkaterületre kell korlátozni. Ha Log Analytics szeretne megnyitni a munkaterület környezetében, válassza a **Azure monitor** menüjének **naplók** elemét. A legutóbb használt munkaterület van kiválasztva, de más munkaterületet is kijelölhet. Lásd: [a naplózási lekérdezés hatóköre és időbeli tartománya Azure Monitor log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Miért kapok hibaüzenetet: "az erőforrás-szolgáltató regisztrálása a Microsoft. reinsights" ehhez az előfizetéshez, hogy engedélyezze ezt a lekérdezést a virtuális gép Log Analytics megnyitásakor? 
Számos erőforrás-szolgáltató automatikusan regisztrálva van, de előfordulhat, hogy manuálisan kell regisztrálnia néhány erőforrás-szolgáltatót. A regisztráció hatóköre mindig az előfizetés. További információ: [Erőforrás-szolgáltatók és típusaik](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Miért nem kapok hibaüzenetet a Log Analytics virtuális gépről való megnyitásakor? 
A virtuális gépek naplófájljainak megtekintéséhez olvasási engedéllyel kell rendelkeznie a virtuálisgép-naplókat tároló munkaterületekhez. Ezekben az esetekben a rendszergazdának Önnek kell megadnia Önnek az Azure-beli engedélyeket.

## <a name="metrics"></a>Mérőszámok

### <a name="why-are-metrics-from-the-guest-os-of-my-azure-virtual-machine-not-showing-up-in-metrics-explorer"></a>Miért nem jelenik meg az Azure-beli virtuális gép vendég operációs rendszerének mérőszámai a mérőszámok Explorerben?
A [platform metrikáit](insights/monitor-azure-resource.md#monitoring-data) automatikusan gyűjti az Azure-erőforrások. Némi konfigurációt kell végrehajtania, ha a virtuális gép vendég operációs rendszerének mérőszámait gyűjti. Windows rendszerű virtuális gépek esetén telepítse a diagnosztikai bővítményt, és konfigurálja a Azure Monitor-gyűjtőt a [Windows Azure Diagnostics bővítmény (wad) telepítése és konfigurálása](platform/diagnostics-extension-windows-install.md)című cikkben leírtak szerint. Linux rendszeren telepítse a következő témakörben ismertetett módon: a [InfluxData-Graf ügynökkel a Linux rendszerű virtuális gépek egyéni metrikáinak gyűjtése](platform/collect-custom-metrics-linux-telegraf.md)című részében leírtak szerint.

## <a name="alerts"></a>Riasztások

### <a name="what-is-an-alert-in-azure-monitor"></a>Mi a riasztás a Azure Monitorban?
A riasztások proaktívan értesítik Önt, ha fontos feltételek találhatók a megfigyelési adataiban. Lehetővé teszik a problémák azonosítását és megcímzését, mielőtt a felhasználók a rendszerértesítéseket. Több fajta riasztás létezik:

- Metrika – a metrika értéke meghaladja a küszöbértéket.
- Napló lekérdezése – a naplózási lekérdezés eredményei megfelelnek a megadott feltételeknek.
- A tevékenység naplója – a műveletnapló eseménye megfelel a megadott feltételeknek.
- Webteszt – a rendelkezésre állási teszt által meghatározott feltételeknek megfelelő eredmények.


Lásd: [Microsoft Azure riasztások áttekintése](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Mi az a műveleti csoport?
A műveleti csoport a riasztások által aktiválható értesítések és műveletek gyűjteménye. Több riasztás is használhat egyetlen műveleti csoportot, amely lehetővé teszi az értesítések és műveletek közös készletének kihasználása. Lásd: [műveleti csoportok létrehozása és kezelése a Azure Portalban](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>Mi az a műveleti szabály?
A műveleti szabályok lehetővé teszik egy adott feltételnek megfelelő riasztások viselkedésének módosítását. Ez lehetővé teszi az ilyen követelmények végrehajtását, mint a riasztási műveletek letiltását a karbantartási időszakban. Egy műveleti csoportot riasztási csoportra is alkalmazhat, ahelyett, hogy közvetlenül a riasztási szabályokra alkalmazná őket. Lásd: [műveleti szabályok](platform/alerts-action-rules.md).

## <a name="agents"></a>Ügynökök

### <a name="does-azure-monitor-require-an-agent"></a>Szükség van Azure Monitor ügynökre?
Az ügynököknek csak az operációs rendszer és a virtuális gépek munkaterhelései adatainak gyűjtésére van szükségük. A virtuális gépek az Azure-ban, egy másik Felhőbeli környezetben vagy a helyszínen is megtalálhatók. Lásd: [a Azure monitor ügynökök áttekintése](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Mi a különbség a Azure Monitor-ügynökök között?
Az Azure diagnosztikai bővítmény az Azure Virtual Machines szolgáltatáshoz kapcsolódik, és adatokat gyűjt Azure Monitor metrikák, az Azure Storage és az Azure Event Hubs számára. A Log Analytics-ügynök az Azure-beli virtuális gépek, egy másik felhőalapú környezet vagy a helyszíni, és adatokat gyűjt Azure Monitor naplókhoz. A függőségi ügynök a Log Analytics ügynök és az összegyűjtött folyamat részleteit és függőségeit igényli. Lásd: [a Azure monitor ügynökök áttekintése](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Használ-e az ügynöki forgalom az ExpressRoute-kapcsolatokat?
A Azure Monitor felé irányuló forgalom a Microsoft peering ExpressRoute áramkört használja. A különböző típusú ExpressRoute-forgalom leírását a [ExpressRoute dokumentációjában](../expressroute/expressroute-faqs.md#supported-services) találja. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Hogyan ellenőrizhető, hogy az Log Analytics ügynök képes-e kommunikálni a Azure Monitorokkal?
Az ügynök számítógépének Vezérlőpultján válassza a **biztonsági & beállítások**, * * Microsoft monitoring Agent lehetőséget. Az **Azure log Analytics (OMS)** lapon a zöld pipa ikon megerősíti, hogy az ügynök képes kommunikálni a Azure monitorokkal. A sárga figyelmeztető ikon azt jelzi, hogy az ügynök problémába ütközik. Ennek egyik gyakori oka, hogy a **Microsoft monitoring Agent** szolgáltatás leállt. A Service Control Manager használatával indítsa újra a szolgáltatást.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Hogyan leállítani a Log Analytics-ügynököt a Azure Monitorsal folytatott kommunikációhoz?
Log Analytics közvetlenül csatlakozó ügynökökhöz nyissa meg a Vezérlőpultot, és válassza a **biztonsági & beállítások**, majd a **Microsoft monitoring Agent** lehetőséget. Az **Azure log Analytics (OMS)** lapon távolítsa el a felsorolt munkaterületeket. A System Center Operations Manager távolítsa el a számítógépet a Log Analytics felügyelt számítógépek listából. Operations Manager frissíti az ügynök konfigurációját, hogy a továbbiakban ne jelentsen Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Mennyibe kerül az adatküldés/ügynök?
Az ügynökök által elküldett adatok mennyisége a következőktől függ:

* Az engedélyezett megoldások
* A begyűjtött naplók és teljesítményszámlálók száma
* A naplókban tárolt adatmennyiség

A részletekért lásd: [a használat és a költségek kezelése Azure monitor naplókkal](platform/manage-cost-storage.md) .

A WireData-ügynököt futtató számítógépek esetében a következő lekérdezéssel tekintheti meg az adatküldés mennyiségét:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Mennyi hálózati sávszélességet használ a Microsoft Management agent (MMA), amikor adatokat küld a Azure Monitor?
A sávszélesség a továbbított adatmennyiség függvénye. Az adat tömörítve van, mert a hálózaton keresztül küldi el a rendszer.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Hogyan lehet értesítést kapni, ha a Log Analytics ügynökből származó adatgyűjtés leáll?

A következő témakörben ismertetett lépéseket követve értesülhet arról, hogy az adatgyűjtés Mikor leáll: [új napló létrehozása riasztás](platform/alerts-metric.md) . Használja a következő beállításokat a riasztási szabályhoz:

- **Riasztási feltétel meghatározása**: adja meg az log Analytics munkaterületet erőforrás-célként.
- **Riasztási feltételek** 
   - **Jel neve**: *egyéni naplók keresése*
   - **Keresési lekérdezés**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Riasztási logika**: az *eredmények száma*, a  **küszöbértéknél** *nagyobb* érték **alapján** *0*
   - **Értékelés alapja**: **időtartam (percben)** *30*, **gyakoriság (perc)** *10*
- **Riasztás részleteinek megadása** 
   - **Név**: *az adatgyűjtés leállt*
   - **Súlyosság**: *Figyelmeztetés*

Válasszon egy meglévő vagy új [műveleti csoportot](platform/action-groups.md) , hogy ha a naplózási riasztás megfelel a feltételeknek, értesítést kap, ha 15 percnél hosszabb szívverés van megadva.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Mik a Azure Monitor ügynökökre vonatkozó tűzfalszabályok?
A tűzfalra vonatkozó követelmények részleteiért lásd a [hálózati tűzfalakra vonatkozó követelményeket](platform/log-analytics-agent.md#network-requirements).


## <a name="visualizations"></a>Vizualizációk

### <a name="why-cant-i-see-view-designer"></a>Miért nem látom a Designer nézetet?

A tervező csak közreműködői engedélyekkel rendelkező felhasználók számára érhető el a Log Analytics munkaterületen.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Konfigurációs problémák
*Nem sikerül beállítani a következőket:*

* [.NET-alkalmazás](app/asp-net-troubleshoot-no-data.md)
* [Már futó alkalmazás figyelése](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure-diagnosztika](platform/diagnostics-extension-to-application-insights.md)
* [Java webalkalmazások](app/java-troubleshoot.md)

*Nem kapok adatok a kiszolgálóról:*

* [Tűzfal-kivételek beállítása](app/ip-addresses.md)
* [ASP.NET-kiszolgáló beállítása](app/monitor-performance-live-website-now.md)
* [Java-kiszolgáló beállítása](app/java-agent.md)

*Hány Application Insights erőforrást kell üzembe helyezni:*

* [A Application Insights üzembe helyezésének megtervezése: egy vagy több Application Insights erőforrás?](app/separate-resources.md)

### <a name="can-i-use-application-insights-with-"></a>Használhatom a Application Insights...?

* [Webalkalmazások egy Azure-beli virtuális gépen vagy Azure-beli virtuálisgép-méretezési csoporton belüli IIS-kiszolgálón](app/azure-vm-vmss-apps.md)
* [Webalkalmazások egy helyi IIS-kiszolgálón vagy egy virtuális gépen](app/asp-net.md)
* [Java-webalkalmazások](app/java-get-started.md)
* [Node.js-alkalmazások](app/nodejs.md)
* [Webalkalmazások az Azure-ban](app/azure-web-apps.md)
* [Cloud Services az Azure-ban](app/cloudservices.md)
* [A Docker-ben futó alkalmazás-kiszolgálók](./azure-monitor-app-hub.yml)
* [Egyoldalas webalkalmazások](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows asztali alkalmazás](app/windows-desktop.md)
* [Más platformok](app/platforms.md)

### <a name="is-it-free"></a>Ingyenes?

Igen, kísérleti felhasználásra. Az alapszintű díjszabás keretében az alkalmazás minden hónapban díjmentesen küldhet bizonyos adatmennyiséget. Az ingyenes juttatás elég nagy a fejlesztéshez, és egy alkalmazás közzététele kis számú felhasználó számára. Beállíthat egy korlátot, amely megakadályozza a megadott mennyiségű adatok feldolgozását.

A GB-nál nagyobb mennyiségű telemetria számítunk fel. Néhány tippet adunk a [díjak korlátozására](app/pricing.md).

A nagyvállalati csomag minden nap esetében díjat számít fel, amelyet minden egyes webkiszolgáló-csomópont telemetria küld. Megfelelő, ha a folyamatos exportálást nagy léptékben kívánja használni.

[Olvassa el a díjszabási tervet](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Mennyibe kerül?

* Nyissa meg a **használati és becsült költségek lapot** egy Application Insights erőforrásban. Van egy diagram a közelmúltbeli használatról. Ha kívánja, beállíthatja az adatmennyiség korlátját.
* Nyissa meg az [Azure számlázási](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) paneljét, és tekintse meg a számlákat az összes erőforráson.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Mit Application Insights módosítani a projektben?
A részletek a projekt típusától függenek. Webalkalmazások esetén:

* Hozzáadja ezeket a fájlokat a projekthez:
  * ApplicationInsights.config
  * ai.js
* A következő NuGet-csomagokat telepíti:
  * *Application INSIGHTS API* – a Core API
  * *Application INSIGHTS API webalkalmazásokhoz* – telemetria küldésére szolgál a kiszolgálóról
  * *Application INSIGHTS API JavaScript-alkalmazásokhoz* – telemetria küldésére szolgál az ügyféltől
* A csomagok közé tartoznak a következő szerelvények:
  * Microsoft. ApplicationInsights
  * Microsoft. ApplicationInsights. platform
* Elemek beszúrása a következőkbe:
  * Web.config
  * packages.config
* (Csak új projektek – ha [Application Insightst ad hozzá egy meglévő projekthez][start], ezt manuálisan kell elvégeznie.) Kódrészleteket szúr be az ügyfél és a kiszolgáló kódjába az Application Insights erőforrás-AZONOSÍTÓval való inicializáláshoz. Egy MVC-alkalmazásban például a kód bekerül a mesteroldalak nézeteibe/Shared/ \_ layout. cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hogyan frissíteni a régebbi SDK-verziókról?
Tekintse meg az SDK [kibocsátási megjegyzéseit](app/release-notes.md) , amelyek megfelelnek az adott alkalmazás típusának.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Hogyan változtathatom meg, hogy a projekt melyik Azure-erőforráshoz küld adatokat?
A Megoldáskezelő kattintson a jobb gombbal, `ApplicationInsights.config` majd válassza a **frissítés Application Insights** lehetőséget. Az Azure-ban egy meglévő vagy új erőforráshoz is elküldheti az adott adatforrást. A frissítési varázsló módosítja a kialakítási kulcsot ApplicationInsights.configban, amely meghatározza, hogy a kiszolgáló SDK hogyan küldje el az adatokat. Ha kijelöli az "összes frissítése" lehetőséget, akkor az azt is megváltoztatja, hogy a kulcs hol jelenik meg a weblapok között.

### <a name="do-new-azure-regions-require-the-use-of-connection-strings"></a>Szükség van új Azure-régiókra a kapcsolatok karakterláncának használatához?

Az új Azure-régiókban a rendszerállapot-kulcsok helyett a kapcsolatok sztringjét **kell** használnia. A [kapcsolódási karakterlánc](./app/sdk-connection-string.md) azonosítja azt az erőforrást, amelyhez hozzá szeretné rendelni a telemetria-adatait. Azt is lehetővé teszi, hogy módosítsa az erőforrás által a telemetria célként használt végpontokat. A kapcsolódási karakterláncot át kell másolnia, és hozzá kell adnia az alkalmazás kódjához vagy egy környezeti változóhoz.

### <a name="can-i-use-providersmicrosoftinsights-componentsapiversions0-in-my-azure-resource-manager-deployments"></a>Használhatom `providers('Microsoft.Insights', 'components').apiVersions[0]` a Azure Resource Manager üzembe helyezéseit?

Ezt a módszert nem ajánlott az API verziójának feltöltésére használni. A legújabb verzió olyan előzetes kiadásokat is tartalmazhat, amelyek feltörési változásokat okozhatnak. Még a nem előzetes verziók újabb kiadásai esetén is, az API-verziók nem mindig visszafelé kompatibilisek a meglévő sablonokkal, vagy bizonyos esetekben előfordulhat, hogy az API-verzió nem érhető el az összes előfizetéshez.

### <a name="what-is-status-monitor"></a>Mi az Állapotfigyelő?

Egy asztali alkalmazás, amelyet az IIS-webkiszolgálóban használhat a Application Insights webalkalmazásokban való konfigurálásához. Nem gyűjt telemetria: leállíthatja, ha nem konfigurál egy alkalmazást. 

[További információ](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Milyen telemetria gyűjtenek Application Insights?

A Server Web appsből:

* HTTP-kérések
* [Függőségek](app/asp-net-dependencies.md). Hívások: SQL-adatbázisok; HTTP-hívások külső szolgáltatásokhoz; Azure Cosmos DB, tábla, blob Storage és üzenetsor. 
* [Kivételek](app/asp-net-exceptions.md) és verem-Nyomkövetések.
* [Teljesítményszámlálók](app/performance-counters.md) – ha [Állapotmonitort](app/monitor-performance-live-website-now.md)használ, a [app Services Azure-figyelést](app/azure-web-apps.md), a [virtuális gép vagy a virtuálisgép-méretezési csoport azure-figyelését](app/azure-vm-vmss-apps.md), vagy a [Application Insights gyűjtött író](app/java-collectd.md).
* [Egyéni események és mérőszámok](app/api-custom-events-metrics.md) .
* [Nyomkövetési naplók](app/asp-net-trace-logs.md) , ha a megfelelő gyűjtőt konfigurálja.

Az [ügyfél weblapjairól](app/javascript.md):

* [Oldalmegtekintések száma](app/usage-overview.md)
* [Ajax-hívások](app/asp-net-dependencies.md) Futó parancsfájlból végrehajtott kérelmek.
* Az oldal nézet betöltési adatkészlete
* Felhasználók és munkamenetek száma
* [Hitelesített felhasználói azonosítók](app/api-custom-events-metrics.md#authenticated-users)

Más forrásokból, ha konfigurálja őket:

* [Azure-diagnosztika](platform/diagnostics-extension-to-application-insights.md)
* [Importálás az Analytics szolgáltatásba](platform/data-collector-api.md)
* [Naplóelemzés](platform/data-collector-api.md)
* [LogStash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kiszűrhetők vagy módosíthatok néhány telemetria?

Igen, a kiszolgálón írhat:

* A telemetria a kiválasztott telemetria-elemekhez tartozó tulajdonságokat szűrheti vagy adja hozzá az alkalmazásból való elküldése előtt.
* A telemetria inicializáló tulajdonságot adhat hozzá a telemetria összes eleméhez.

További információ a [ASP.net](app/api-filtering-sampling.md) és a [Java](app/java-filter-telemetry.md)-ról.

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Hogyan számítják ki a város, az ország/régió és az egyéb földrajzi hely adatértékét?

A [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)használatával megkeresjük a webes ügyfél IP-címét (IPv4 vagy IPv6).

* Böngésző telemetria: összegyűjtjük a küldő IP-címét.
* Kiszolgáló telemetria: a Application Insights modul gyűjti az ügyfél IP-címét. Ha be van állítva, a rendszer nem gyűjti `X-Forwarded-For` .
* Ha többet szeretne megtudni arról, hogy az IP-cím és a térinformatikai adatok hogyan lesznek begyűjtve Application Insights tekintse meg ezt a [cikket](./app/ip-collection.md).

Beállíthatja `ClientIpHeaderTelemetryInitializer` , hogy az IP-cím más fejlécből legyen végrehajtva. Egyes rendszerekben például egy proxy, egy terheléselosztó vagy egy CDN helyezi át őket `X-Originating-IP` . [További információ](https://apmtips.com/posts/2016-07-05-client-ip-address/).

A [Power bi](app/export-power-bi.md ) segítségével megjelenítheti a kérések telemetria egy térképen.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Mennyi ideig tartanak a portálon tárolt adat? Biztonságos?
Vessen egy pillantást az [adatok megőrzésére és az adatvédelemre][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Mi történik az alkalmazás-betekintési telemetria, ha egy kiszolgáló vagy eszköz elveszti az Azure-ral való kapcsolódást?

Az összes SDK, beleértve a web SDK-t is, "megbízható átvitel" vagy "robusztus szállítás". Ha a kiszolgáló vagy az eszköz elveszti a kapcsolatot az Azure-val, a telemetria [a fájlrendszeren](./app/data-retention-privacy.md#does-the-sdk-create-temporary-local-storage) (Server SDK-k) vagy a HTML5 munkamenet-tárolóban (web SDK) helyileg tárolja a rendszer. Az SDK rendszeres időközönként újra megpróbálja elküldeni ezt a telemetria, amíg a betöltési szolgáltatás nem tekinti az "elavult" (48 órás naplók, 30 perc a metrikák esetében). A rendszer elveti az elavult telemetria. Bizonyos esetekben, például ha a helyi tárterület megtelt, a rendszer nem hajtja végre az újrapróbálkozást.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>A személyes adatküldés a telemetria?

Ez akkor lehetséges, ha a kód ilyen adatokat küld. Akkor is előfordulhat, ha a stack-nyomkövetésben szereplő változók személyes adatba tartoznak. A fejlesztési csapatnak kockázatértékelést kell végeznie, hogy a személyes adatai megfelelően legyenek kezelve. [További információ az adatmegőrzésről és az adatvédelemről](app/data-retention-privacy.md).

Az ügyfél-webcímek **minden** oktettje mindig 0-ra van állítva a földrajzi hely attribútumainak megvizsgálása után.

A [Application Insights JavaScript SDK](app/javascript.md) alapértelmezés szerint nem tartalmazza az összes személyes adatforrást. Az alkalmazásban használt egyes személyes adatok azonban az SDK-ban is felvehetők (például teljes nevek `window.title` vagy fiókazonosító az x/óra URL-lekérdezési paraméterekben). Egyéni személyes adatmaszkolás esetén adjon hozzá egy [telemetria-inicializálást](app/api-filtering-sampling.md#javascript-web-applications).

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>A saját kialakítási kulcs látható a saját weblap forrásában.

* Ez a figyelési megoldások általános gyakorlata.
* Nem használható az adatai ellopására.
* Felhasználhatja az adatait, vagy riasztásokat indíthat.
* Nem tudtuk meghallgatni, hogy minden ügyfélnek ilyen problémája volt.

Ön ilyenkor az alábbiakat teheti:

* Az ügyfél-és a kiszolgálói adatforrásokhoz két különálló rendszerkulcs (külön Application Insights erőforrás) használata szükséges. Vagy
* Írjon be egy proxyt, amely a kiszolgálón fut, és hogy a webes ügyfél az adott proxyn keresztül küldje el az adatait.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Hogyan lásd: az adatposta a diagnosztikai keresésben?
A rendszer nem naplózza az adatpostát, de TrackTrace hívást is használhat: az üzenet paraméterében elhelyezheti az adatbevitelt. Ez hosszabb mérethatárt, mint a karakterlánc-tulajdonságok korlátai, de nem szűrheti.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Egy vagy több Application Insights-erőforrást használok?

Egyetlen erőforrás használata egyetlen üzleti rendszeren lévő összes összetevőhöz vagy szerepkörhöz. Külön erőforrásokat használhat a fejlesztési, tesztelési és kiadási verziókhoz, valamint a független alkalmazásokhoz.

* [Itt tekintheti meg a vitát](app/separate-resources.md)
* [Példa – felhőalapú szolgáltatás munkavégző és webes szerepkörökkel](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hogyan dinamikusan módosítja a kialakítási kulcsot?

* [Vitafórum](app/separate-resources.md)
* [Példa – felhőalapú szolgáltatás munkavégző és webes szerepkörökkel](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Mik a felhasználók és a munkamenetek száma?

* A JavaScript SDK beállítja a felhasználói cookie-t a webes ügyfélen, a visszatérő felhasználók azonosítására, valamint egy munkamenet-cookie-t a tevékenységek csoportosítására.
* Ha nincs ügyféloldali parancsfájl, beállíthatja [a cookie-kat a kiszolgálón](https://apmtips.com/posts/2016-07-09-tracking-users-in-api-apps/).
* Ha egy valós felhasználó különböző böngészőkben használja a webhelyét, vagy ha privát vagy inkognitóbani böngészést vagy különböző gépeket használ, a rendszer egynél többször veszi fel őket.
* A bejelentkezett felhasználók számítógépek és böngészők közötti azonosításához vegyen fel egy hívást a [setAuthenticatedUserContext ()](app/api-custom-events-metrics.md#authenticated-users)szolgáltatásba.

### <a name="how-does-application-insights-generate-device-information-browser-os-language-model"></a>Hogyan hoz Application Insights az eszköz adatait (böngésző, operációs rendszer, nyelv, modell)?

A böngésző átadja a felhasználói ügynök sztringjét a kérelem HTTP-fejlécében, a Application Insights betöltési szolgáltatás pedig [ua-elemzőt](https://github.com/ua-parser/uap-core) használ az adattáblákban és a élményekben látható mezők létrehozásához. Ennek eredményeképpen Application Insights felhasználók nem módosíthatják ezeket a mezőket.

Esetenként előfordulhat, hogy az adatok hiányoznak vagy pontatlanok, ha a felhasználó vagy a vállalat letiltja a felhasználói ügynök küldését a böngésző beállításaiban. Emellett előfordulhat, hogy az [ua-elemző regexek](https://github.com/ua-parser/uap-core/blob/master/regexes.yaml) nem tartalmazzák az összes eszközre vonatkozó információt, vagy a Application Insights nem fogadták el a legújabb frissítéseket.

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Engedélyeztem mindent Application Insights?
| Mit kell látnia? | Útmutató | Miért szeretné |
| --- | --- | --- |
| Rendelkezésre állási diagramok |[Webes tesztek](app/monitor-web-app-availability.md) |Ismerje meg a webalkalmazását |
| Server app Perf: válaszidő,... |[Application Insights hozzáadása a projekthez](app/asp-net.md) vagy [AI-Állapotmonitor telepítése a kiszolgálón](app/monitor-performance-live-website-now.md) (vagy saját kód írása a [függőségek nyomon követéséhez](app/api-custom-events-metrics.md#trackdependency)) |A Teljesítményfigyelő hibáinak észlelése |
| Függőségi telemetria |[AI-Állapotmonitor telepítése a kiszolgálón](app/monitor-performance-live-website-now.md) |Az adatbázisokkal vagy más külső összetevőkkel kapcsolatos problémák diagnosztizálása |
| Verem nyomkövetésének lekérése kivételekről |[TrackException-hívások beszúrása a kódban](app/asp-net-exceptions.md) (de néhányat automatikusan kell jelenteni) |Kivételek észlelése és diagnosztizálása |
| Keresési naplók nyomkövetése |[Naplózási adapter hozzáadása](app/asp-net-trace-logs.md) |Kivételek diagnosztizálása, Perf-problémák |
| Az ügyfél használatának alapjai: oldalletöltések, munkamenetek,... |[JavaScript-inicializálás a weblapokon](app/javascript.md) |Használatelemzés |
| Ügyfél egyéni metrikái |[Hívások követése a weblapokon](app/api-custom-events-metrics.md) |Felhasználói élmény fokozása |
| Kiszolgáló egyéni metrikái |[Hívások követése a kiszolgálón](app/api-custom-events-metrics.md) |Üzleti intelligencia |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Miért nem egyenlő a keresési és a metrikák diagramjai?

A [mintavétel](app/sampling.md) csökkenti az alkalmazásból a portálra ténylegesen küldött telemetria elemek (kérelmek, egyéni események stb.) számát. A keresés területen láthatja a ténylegesen fogadott elemek számát. Az események számát megjelenítő mérőszám-diagramoknál megtekintheti a bekövetkezett eredeti események számát. 

Minden továbbított tétel egy olyan `itemCount` tulajdonságot hordoz, amely azt mutatja, hogy hány eredeti eseményt képvisel az adott tétel. A mintavétel működés közbeni megfigyeléséhez futtassa ezt a lekérdezést az Analyticsben:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```

### <a name="how-do-i-move-an-application-insights-resource-to-a-new-region"></a>Hogyan egy Application Insights erőforrást egy új régióba?

A meglévő Application Insights erőforrások egyik régióból a másikba való áthelyezése **jelenleg nem támogatott**. Az összegyűjtött korábbi adatok **nem telepíthetők át** új régióba. Az egyetlen részleges Áthidaló megoldás a következő:

1. Új Application Insights-erőforrás létrehozása ([klasszikus](app/create-new-resource.md) vagy [munkaterület-alapú](./app/create-workspace-resource.md)) az új régióban.
2. Hozza létre újra az új erőforrás eredeti erőforrásához tartozó összes egyedi testreszabást.
3. Módosítsa az alkalmazást úgy, hogy az új régió-erőforrás kialakítási [kulcsát](app/create-new-resource.md#copy-the-instrumentation-key) vagy a [kapcsolódási karakterláncot](app/sdk-connection-string.md)használja.  
4. Ellenőrizze, hogy minden továbbra is a várt módon működik-e az új Application Insights erőforrással. 
5. Ezen a ponton törölheti az eredeti erőforrást, amely az **összes korábbi adatvesztést** eredményezi. Vagy megtarthatja az eredeti erőforrást az adatmegőrzési beállítások időtartamára visszamenőleges jelentéskészítés céljából.

Az új régióban az erőforráshoz gyakran manuálisan újra létre kell hozni vagy frissíteni kell az egyedi testreszabásokat, amelyek azonban nem korlátozódnak a következőkre:

- Hozza létre újra az egyéni irányítópultokat és munkafüzeteket. 
- Hozza létre újra vagy frissítse az egyéni log/metrikus riasztások hatókörét. 
- Rendelkezésre állási riasztások újbóli létrehozása.
- Hozza létre újra a felhasználók számára az új erőforrás eléréséhez szükséges egyéni Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) beállításokat. 
- Replikálja a betöltési mintavételezést, az adatmegőrzést, a napi korlátot és az egyéni metrikák engedélyezését érintő beállításokat. Ezeket a beállításokat a **használati és becsült költségek** panelen szabályozhatja.
- Minden olyan integráció, amely az API-kulcsokra támaszkodik, például a [kibocsátási jegyzetek](./app/annotations.md), az [élő metrika biztonságos vezérlési csatornája](app/live-stream.md#secure-the-control-channel) stb. Új API-kulcsokat kell létrehoznia, és frissítenie kell a társított integrációt. 
- A klasszikus erőforrások folyamatos exportálását újra kell konfigurálni.
- A munkaterület-alapú erőforrások diagnosztikai beállításait újra kell konfigurálni.

> [!NOTE]
> Ha egy új régióban létrehozott erőforrás lecserél egy klasszikus erőforrást, javasoljuk, hogy vizsgálja meg az [Új munkaterület-alapú erőforrás létrehozásának](app/create-workspace-resource.md) előnyeit, vagy [a meglévő erőforrást a munkaterület-alapú áttelepítéssel](app/convert-classic-resource.md). 

### <a name="automation"></a>Automation

#### <a name="configuring-application-insights"></a>Application Insights konfigurálása

A PowerShell-szkripteket az Azure erőforrás-figyelő használatával is [megírhatja](app/powershell.md) :

* Application Insights erőforrások létrehozása és frissítése.
* Állítsa be a díjszabási tervet.
* Szerezze be a kialakítási kulcsot.
* Metrikai riasztás hozzáadása.
* Adja meg a rendelkezésre állási tesztet.

Nem állítható be metrikai Explorer-jelentés, vagy nem állítható be folyamatos exportálás.

#### <a name="querying-the-telemetry"></a>A telemetria lekérdezése

[Analytics](./log-query/log-query-overview.md) -lekérdezések futtatásához használja a [REST API](https://dev.applicationinsights.io/) .

### <a name="how-can-i-set-an-alert-on-an-event"></a>Hogyan állíthatok be riasztást eseményre?

Az Azure-riasztások csak mérőszámokon alapulnak. Hozzon létre egy egyéni mérőszámot, amely egy érték küszöbértékét adja meg, amikor az esemény bekövetkezik. Ezután állítson be egy riasztást a metrikán. Értesítést kap, ha a metrika mindkét irányban átlépi a küszöbértéket; nem kap értesítést az első átlépésig, függetlenül attól, hogy a kezdeti érték magas vagy alacsony; a késések mindig néhány percet vesznek igénybe.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Vannak adatátviteli díjak az Azure-webalkalmazások és a Application Insights között?

* Ha az Azure-webalkalmazás egy olyan adatközpontban található, amelyben Application Insights gyűjtemény végpontja található, díjmentesen használható. 
* Ha nem található gyűjteményi végpont a gazdagép adatközpontjában, akkor az alkalmazás telemetria az Azure-beli [kimenő díjakat](https://azure.microsoft.com/pricing/details/bandwidth/)is felmerül.

Ez nem függ attól, hogy hol található a Application Insights-erőforrás. Csak a végpontok eloszlása függ.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Küldhetek telemetria az Application Insights portálra?

Javasoljuk, hogy használja az SDK-kat, és használja az [SDK API](app/api-custom-events-metrics.md)-t. Különböző [platformokon](app/platforms.md)léteznek az SDK különféle változatai. Ezek az SDK-k pufferelést, tömörítést, szabályozást, újrapróbálkozást és így tovább kezelik. A betöltési [séma](https://github.com/microsoft/ApplicationInsights-dotnet/tree/master/BASE/Schema/PublicSchema) és a [végpont protokoll](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) azonban nyilvános.

### <a name="can-i-monitor-an-intranet-web-server"></a>Nyomon követhető az intranetes webkiszolgáló?

Igen, de engedélyeznie kell a szolgáltatásoknak a tűzfal-kivételek vagy a proxy-átirányítások által nyújtott forgalmat.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Tekintse át a szolgáltatások és IP-címek teljes listáját [itt](app/ip-addresses.md).

#### <a name="firewall-exception"></a>Tűzfal-kivétel

Lehetővé teszi, hogy a webkiszolgáló telemetria küldjön a végpontoknak. 

#### <a name="gateway-redirect"></a>Átjáró átirányítása

Átirányíthatja a forgalmat a kiszolgálóról az intranetes átjáróra a konfigurációban található végpontok felülírásával. Ha ezek a "végpont" tulajdonságok nem szerepelnek a konfigurációban, ezek az osztályok az alábbi példában látható alapértelmezett értékeket fogják használni ApplicationInsights.config. 

Az átjárónak a végpont alapcímeihez kell irányítani a forgalmat. A konfigurációban cserélje le az alapértelmezett értékeket a értékre `http://<your.gateway.address>/<relative path>` .


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Példa ApplicationInsights.config alapértelmezett végpontokkal:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> A ApplicationIdProvider a v 2.6.0-től kezdődően érhető el.



#### <a name="proxy-passthrough"></a>Proxy átengedése

A proxy továbbítása a gépi vagy az alkalmazás szintű proxy konfigurálásával érhető el.
További információ: a [DefaultProxy](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)-ra vonatkozó DotNet-cikk.
 
 Példa Web.config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Futtathatok rendelkezésre állási webes teszteket egy intranetes kiszolgálón?

A [webes tesztek](app/monitor-web-app-availability.md) a világ minden részén elosztott, jelenléti pontokon futnak. Két megoldás létezik:

* Tűzfal ajtaja – engedélyezze a kérelmeket [a kiszolgálónak a webes tesztek hosszú és módosítható listájáról](app/ip-addresses.md).
* Saját kód megírásával rendszeres kérelmeket küldhet a kiszolgálónak az intraneten belülről. A Visual Studio webes tesztek erre a célra is futtathatók. A Tester a TrackAvailability () API használatával küldheti el az eredményeket Application Insights.

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Mennyi időt vesz igénybe a telemetria gyűjtése?

A legtöbb Application Insights adat 5 perces késéssel rendelkezik. Egyes adatsorok hosszabb időt vehetnek igénybe; általában nagyobb naplófájlok. További információ: [Application INSIGHTS SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md

### <a name="http-502-and-503-responses-are-not-always-captured-by-application-insights"></a>A HTTP 502 és a 503 választ nem mindig rögzíti Application Insights

a "502 hibás átjáró" és "503 szolgáltatás nem érhető el" hibaüzeneteket a Application Insights nem mindig rögzíti. Ha csak ügyféloldali JavaScript van használatban a figyeléshez, ez a várt viselkedés, mivel a rendszer a hibaüzenetet a HTML-fejlécet tartalmazó oldal előtt adja vissza. 

Ha az 502-es vagy a 503-es választ egy kiszolgálóoldali figyelést engedélyező kiszolgálóról küldték, a hibákat a Application Insights SDK fogja gyűjteni. 

Azonban még mindig vannak olyan esetek, amikor a kiszolgálóoldali figyelés engedélyezve van egy alkalmazás webkiszolgálóján, hogy a Application Insights nem rögzíti a 502 vagy 503 hibát. Számos modern webkiszolgáló nem teszi lehetővé, hogy az ügyfelek közvetlenül kommunikáljanak egymással, hanem olyan megoldásokat alkalmaznak, mint a fordított proxyk, amelyekkel az ügyfél és az előtér-webkiszolgálók között oda-vissza továbbíthatja az adatokat. 

Ebben a forgatókönyvben egy 502-es vagy 503-os választ lehet visszaadni az ügyfélnek egy fordított proxys rétegbeli probléma miatt, és ezt a Application Insights nem rögzíti. Ennek a rétegnek a problémáinak észlelése érdekében előfordulhat, hogy a fordított proxyról át kell küldenie a naplókat Log Analytics és létre kell hoznia egy egyéni szabályt a 502/503-válaszok ellenőrzéséhez. Ha többet szeretne megtudni a 502-es és a 503-es hibák gyakori okairól, tekintse meg a ["502 Bad Gateway" és a "503 szolgáltatás nem érhető el" című Azure app Service hibaelhárítási cikkét](../app-service/troubleshoot-http-502-http-503.md).     


## <a name="opentelemetry"></a>OpenTelemetry

### <a name="what-is-opentelemetry"></a>Mi az a OpenTelemetry?

Új, nyílt forráskódú szabvány a Megfigyelhetőség terén. További információ: [https://opentelemetry.io/](https://opentelemetry.io/) .

### <a name="why-is-microsoft--azure-monitor-investing-in-opentelemetry"></a>Miért van a Microsoft/Azure Monitor befektetés a OpenTelemetry-ben?

Úgy véljük, hogy az ügyfeleket három okból ajánljuk fel:
   1. Több ügyfél-forgatókönyv támogatásának engedélyezése.
   2. Nem kell megvárnia a szállítói zárolást.
   3. Növelje ügyfelei átláthatóságát és elkötelezettségét.

Emellett a Microsoft stratégiáját is összehangolja a [nyílt forráskód felkarolása](https://opensource.microsoft.com/)érdekében.

### <a name="what-additional-value-does-opentelemetry-give-me"></a>Milyen további értéket ad a OpenTelemetry?

A fenti okok mellett a OpenTelemetry hatékonyabban méretezhető, és konzisztens tervezési/konfigurációs lehetőségeket biztosít a különböző nyelveken.

### <a name="how-can-i-test-out-opentelemetry"></a>Hogyan lehet kipróbálni a OpenTelemetry?

Regisztráljon, és csatlakozzon a Azure Monitor Application Insights korai bevezetési közösséghez [https://aka.ms/AzMonOtel](https://aka.ms/AzMonOtel) .

### <a name="what-does-ga-mean-in-the-context-of-opentelemetry"></a>Mit jelent a GA a OpenTelemetry kontextusában?

A [OpenTelemetry-Közösség](https://medium.com/opentelemetry/ga-planning-f0f6d7b5302)általánosan elérhetővé (GA) határoz meg. A "GA" OpenTelemetry azonban nem azt jelenti, hogy a meglévő Application Insights SDK-k esetében a szolgáltatás paritása szerepel. A Azure Monitor továbbra is javasolja a jelenlegi Application Insights SDK-kat azon ügyfeleinknek, akik olyan szolgáltatásokat igényelnek, mint például az [előre aggregált mérőszámok](app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics), az [élő metrikák](app/live-stream.md), az [adaptív mintavételezés](app/sampling.md#adaptive-sampling), a [Profiler](app/profiler-overview.md)és a [Snapshot Debugger](app/snapshot-debugger.md) , amíg a OpenTelemetry SDK-k elérnék a szolgáltatás lejáratát

### <a name="can-i-use-preview-builds-in-production-environments"></a>Használhatom az előzetes verziójú buildeket éles környezetekben?

Nem ajánlott. További információért lásd a [Microsoft Azure előzetes verziójának kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

### <a name="whats-the-difference-between-opentelemetry-sdk-and-auto-instrumentation"></a>Mi a különbség a OpenTelemetry SDK és az automatikus Instrumentation között?

Az OpenTelemetry specifikációja az [SDK](https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/glossary.md#telemetry-sdk)-t határozza meg. Röviden, az "SDK" egy nyelvfüggő csomag, amely összegyűjti az telemetria adatokat az alkalmazás különböző összetevői között, és elküldi az adatokat Azure Monitor egy exportőr használatával.

Az automatikus kiépítés fogalma (más néven bytecode-befecskendezés, kód nélküli vagy ügynök-alapú) arra a képességre utal, hogy az alkalmazást a kód módosítása nélkül lehessen felvenni. További információért tekintse meg például a [OpenTelemetry Java automatikus](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/master/README.md) rendszerállapot-dokumentációját.

### <a name="whats-the-opentelemetry-collector"></a>Mi a OpenTelemetry-gyűjtő?

A OpenTelemetry-gyűjtőt a [GitHub információs fájlja](https://github.com/open-telemetry/opentelemetry-collector#opentelemetry-collector)írja le. Jelenleg a Microsoft nem használja a OpenTelemetry-gyűjtőt, és a Azure Monitor Application Insights küldött közvetlen exportőröktől függ.

### <a name="whats-the-difference-between-opencensus-and-opentelemetry"></a>Mi a különbség a OpenCensus és a OpenTelemetry között?

A [OpenCensus](https://opencensus.io/) az [OpenTelemetry](https://opentelemetry.io/)előfutára. A Microsoft segített összekapcsolni a [OpenTracing](https://opentracing.io/) és a OpenCensus-t, hogy OpenTelemetry hozzon létre, amely a világ egyetlen megfigyelhető szabványa. Azure Monitor jelenlegi [éles környezetben – az ajánlott PYTHON SDK](app/opencensus-python.md) a OpenCensus-on alapul, de végül az összes Azure monitor SDK-k a OpenTelemetry-on alapulnak.


## <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz

### <a name="what-does-other-processes-represent-under-the-node-view"></a>Mit jelent a *többi folyamat* a csomópont nézet alatt?

A **többi folyamat** célja, hogy könnyebben megértse a csomópont magas erőforrás-használatának kiváltó okát. Ez lehetővé teszi a tárolók és a nem tároló folyamatok közötti használat megkülönböztetését.

Mik ezek a **más folyamatok**? 

Ezek a nem tároló folyamatok, amelyek a csomóponton futnak.  

Hogyan számítjuk ki ezt?

**Egyéb folyamatok**  =  *Teljes használat a CAdvisor*  -  *Használat a tároló folyamatból*

A **többi folyamat** az alábbiakat tartalmazza:

- Önállóan felügyelt vagy felügyelt Kubernetes nem tároló folyamatok 

- Tároló futásidejű folyamatai  

- Kubelet  

- A csomóponton futó rendszerfolyamatok 

- Csomópontok hardverén vagy virtuális gépen futó egyéb nem Kubernetes számítási feladatok 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Nem látom a ContainerLog tábla lekérdezése során kitöltött képek és nevek tulajdonság értékét.

Az ügynök verziójának ciprod12042019 és újabb verzióiban alapértelmezés szerint ez a két tulajdonság nem töltődik fel minden egyes naplófájlhoz, hogy csökkentse a begyűjtött naplózási adatokhoz felmerülő költségeket. Az alábbi két lehetőség közül választhat a tulajdonságokat tartalmazó tábla lekérdezéséhez:

#### <a name="option-1"></a>1\. lehetőség 

Csatlakozás más táblákhoz, hogy ezek a tulajdonságértékek szerepeljenek az eredmények között.

Módosítsa a lekérdezéseket úgy, hogy a ```ContainerInventory``` táblázatból a ContainerID tulajdonsághoz való csatlakozással képet és ImageTag tulajdonságokat is tartalmazzon. A Name (név) tulajdonságot belefoglalhatja a ```ContainerLog``` KubepodInventory tábla ContaineName mezőjéből a ContainerID tulajdonsághoz való csatlakozással. Ez az ajánlott lehetőség.

A következő példa egy példaként szolgáló részletes lekérdezést tartalmaz, amely ismerteti, hogyan lehet beolvasni ezeket a mezőértékeket az illesztésekkel.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>2\. lehetőség

Engedélyezze újra a gyűjteményt ezen tulajdonságok esetében minden egyes tároló-naplófájlhoz.

Ha az első lehetőség a lekérdezés módosítása miatt nem megfelelő, akkor az ```log_collection_settings.enrich_container_logs``` [adatgyűjtési konfigurációs beállítások](insights/container-insights-agent-config.md)részben leírtak szerint engedélyezheti a mezők begyűjtését az ügynök konfigurációs térképének beállításával.

> [!NOTE]
> A második lehetőség nem ajánlott olyan nagyméretű fürtök esetében, amelyek több mint 50 csomóponttal rendelkeznek, mert az API-kiszolgáló hívásokat kezdeményez a fürt minden csomópontján, hogy elvégezze ezt a dúsítást. Ez a beállítás az adatok méretét is növeli minden összegyűjtött naplófájl esetében.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Megtekinthetem a Grafana összegyűjtött mérőszámokat?

A tárolók Azure Monitor támogatja a Grafana-irányítópultokon a Log Analytics munkaterületen tárolt mérőszámok megtekintését. A Grafana [irányítópult-tárházból](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) letölthető sablon segítségével elsajátíthatja az első lépéseket, és megtudhatja, hogyan kérdezheti le a figyelt fürtök további adatait az egyéni Grafana-irányítópultok megjelenítéséhez. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Megfigyelhető az AK-motor fürtje Azure Monitor for containers használatával?

A tárolók Azure Monitor támogatja az Azure-ban üzemeltetett, AK-motor (korábbi nevén ACS-motor) fürt (ek) ben üzembe helyezett, a tároló munkaterheléseit. További részletek és áttekintés a forgatókönyv figyelésének engedélyezéséhez szükséges lépésekről: a [Azure monitor használata a tárolók számára az AK-motorhoz](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Miért nem láthatók a Log Analytics munkaterület adatai?

Ha az adatok minden nap egy adott időpontban nem jelennek meg a Log Analytics-munkaterületen, előfordulhat, hogy elérte az alapértelmezett 500 MB-os vagy a naponta gyűjthető adatok mennyiségének szabályozására szolgáló napi korlátot. Ha elérte a napi korlátot, az adatgyűjtés leáll, és csak a következő napon folytatódik. Tekintse át az adatfelhasználást, és frissítsen egy másik díjszabási csomagra a várt használati szokások alapján: az [adatok használatának és költségének naplózása](platform/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Mik a ContainerInventory táblában megadott tárolók állapotai?

A ContainerInventory tábla a leállított és futó tárolókkal kapcsolatos információkat tartalmaz. A tábla az ügynökön belüli munkafolyamattal van feltöltve, amely lekérdezi a Docker-t az összes tárolónál (futó és leállított), és továbbítja az adatokat a Log Analytics munkaterületen.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hogyan a *hiányzó előfizetés-regisztrációs* hiba elhárítása?

Ha a **Microsoft. OperationsManagement előfizetés-regisztrációja hiányzik**, akkor a Microsoft. OperationsManagement erőforrás-szolgáltató regisztrálása az előfizetésben, ahol a munkaterület meg van adva, a **Microsoft.** . Ennek a dokumentációja [itt](../azure-resource-manager/templates/error-register-resource-provider.md)található.

### <a name="is-there-support-for-kubernetes-rbac-enabled-aks-clusters"></a>Támogatja a Kubernetes RBAC-kompatibilis AK-fürtöket?

A tároló-figyelési megoldás nem támogatja a Kubernetes RBAC, de a tárolók esetében Azure Monitor is támogatott. Előfordulhat, hogy a megoldás részleteit tartalmazó lap nem jeleníti meg a megfelelő információkat a fürtök adatait megjelenítő pengék között.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Hogyan lehetővé teszi a naplók gyűjtését a Kube-System névtérben a Helm használatával?

Alapértelmezés szerint le van tiltva a Kube-rendszernévtérben lévő tárolók naplójának gyűjteménye. A omsagent egy környezeti változó beállításával engedélyezhető a naplók gyűjteménye. További információ: [Azure monitor for containers](https://aka.ms/azuremonitor-containers-helm-chart) GitHub oldal. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Hogyan frissíteni a omsagent a legújabb kiadású verzióra?

Az ügynök frissítésének megismeréséhez tekintse meg az [ügynökök kezelése](insights/container-insights-manage-agent.md)című témakört.

### <a name="how-do-i-enable-multi-line-logging"></a>Hogyan a többsoros naplózás engedélyezése?

A tárolók számára jelenleg Azure Monitor nem támogatja a többsoros naplózást, de vannak elérhető áthidaló megoldások. Az összes szolgáltatást JSON formátumban is konfigurálhatja, majd a Docker/Moby egyetlen sorba írja azokat.

Például becsomagolhatja a naplót JSON-objektumként, ahogy az alábbi példában is látható egy példa node.js alkalmazáshoz:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Az alábbi példához hasonlóan a naplók esetében a következő példa jelenik meg Azure Monitorban:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

A probléma részletes megtekintéséhez tekintse meg a következő GitHub- [hivatkozást](https://github.com/moby/moby/issues/22920).

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Az élő naplók engedélyezésekor Hogyan az Azure AD-hibák elhárítása? 

A következő hibaüzenet jelenhet meg: a **kérelemben megadott válasz URL-cím nem egyezik az alkalmazáshoz konfigurált válasz URL-címekkel: "<Application ID \> "**. A megoldás megoldása a következő cikkben található: a [tárolók információinak valós idejű megtekintése Azure monitor a tárolók](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)használatával. 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Miért nem tudom frissíteni a fürtöt a bevezetést követően?

Ha egy AK-fürthöz engedélyezte a Azure Monitort a tárolók számára, akkor törölje azt a Log Analytics-munkaterületet, amelyhez a fürt az adatokat küldi, amikor a fürt frissítésére tett kísérlet során sikertelen lesz. Ennek megkerüléséhez le kell tiltania a figyelést, majd újra engedélyeznie kell, hogy az előfizetés egy másik érvényes munkaterületre hivatkozik. Ha újra megpróbálja végrehajtani a fürt frissítését, akkor a folyamatnak sikeresen fel kell dolgoznia és el kell végeznie a műveletet.  

### <a name="which-ports-and-domains-do-i-need-to-openallow-for-the-agent"></a>Mely portokra és tartományokra van szükségem az ügynök megnyitásához/engedélyezéséhez?

Tekintse meg a [hálózati tűzfalra vonatkozó követelményeket](insights/container-insights-onboard.md#network-firewall-requirements) a proxyra és a tűzfalra vonatkozó konfigurációs információkhoz, amelyek a tároló ügynökhöz szükségesek az Azure, az Azure US government és az Azure China 21Vianet-felhők esetében.


## <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez

### <a name="can-i-onboard-to-an-existing-workspace"></a>Bejelentkezhetek egy meglévő munkaterületre?
Ha a virtuális gépek már csatlakoznak egy Log Analytics-munkaterülethez, akkor továbbra is használhatja ezt a munkaterületet, ha Azure Monitor for VMsbe bevezetésre kerül, amennyiben az a [támogatott régiók](insights/vminsights-configure-workspace.md#supported-regions)egyikében található.


### <a name="can-i-onboard-to-a-new-workspace"></a>Bejelentkezhetek egy új munkaterületre? 
Ha a virtuális gépek jelenleg nem kapcsolódnak meglévő Log Analytics-munkaterülethez, létre kell hoznia egy új munkaterületet az adatai tárolásához. Az új alapértelmezett munkaterület létrehozása automatikusan történik, ha egyetlen Azure-beli virtuális gépet állít be Azure Monitor for VMs a Azure Portal keresztül.

Ha úgy dönt, hogy a parancsfájl-alapú módszert használja, ezeket a lépéseket a [Azure monitor for VMS engedélyezése Azure PowerShell vagy Resource Manager-sablon használatával](./insights/vminsights-enable-powershell.md) című cikkben tárgyaljuk. 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Mi a teendő, ha a virtuális gép már jelent egy meglévő munkaterületet?
Ha már begyűjti az adatait a virtuális gépekről, lehetséges, hogy már konfigurálta az adatok jelentését egy meglévő Log Analytics-munkaterületre.  Ha a munkaterület az egyik támogatott régióban található, akkor a Azure Monitor for VMst engedélyezheti a meglévő munkaterületen.  Ha a már használt munkaterület nem a támogatott régiókban található, akkor a Azure Monitor for VMs jelenleg nem fog tudni bejelentkezni.  Aktívan dolgozunk a további régiók támogatásán.


### <a name="why-did-my-vm-fail-to-onboard"></a>Miért nem sikerült bejelentkezni a virtuális gépre?
Amikor Azure-beli virtuális gépet telepít a Azure Portalból, a következő lépések történnek:

* Ha ez a beállítás be van jelölve, egy alapértelmezett Log Analytics munkaterület jön létre.
* A Log Analytics ügynök virtuálisgép-bővítmény használatával települ az Azure-beli virtuális gépekre, ha azt meg kell határozni.  
* A Azure Monitor for VMs Map-függőségi ügynök az Azure-beli virtuális gépeken bővítmény használatával van telepítve, ha azt meg kell határozni. 

A bevezetési folyamat során a fent ismertetett állapotot vizsgáljuk, hogy az értesítési állapotot a portálon vissza lehessen adni. A munkaterület és az ügynök telepítésének konfigurálása általában 5 – 10 percet vesz igénybe. A figyelési információk megtekintése a portálon további 5 – 10 percet vesz igénybe.  

Ha kezdeményezte a bevezetést, és megtekinti, hogy a virtuális gépet be kell-e állítani, akkor akár 30 percet is igénybe vehet, amíg a virtuális gép elvégezheti a folyamatot. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Nem látok olyan információt, amely a virtuális gép teljesítmény-diagramjaiban található
A teljesítmény-diagramok frissítve lettek a *InsightsMetrics* táblában tárolt adatszolgáltatások használatára.  Ha szeretné megtekinteni az ezekben a diagramokban lévőket, frissítenie kell az új VM-elemzési megoldás használatára.  További információkért tekintse meg a [GA GYIK](insights/vminsights-ga-release-faq.md) -t.

Ha nem látja a teljesítményadatokat a lemez táblában vagy egyes teljesítmény-diagramoknál, akkor előfordulhat, hogy a teljesítményszámlálók nem konfigurálhatók a munkaterületen. A megoldáshoz futtassa a következő [PowerShell-szkriptet](./insights/vminsights-enable-powershell.md).


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Miben különbözik az Azure Monitor for VMs Map funkció a Service Maptól?
Az Azure Monitor for VMs-leképezési funkció Service Mapon alapul, de a következő különbségek vannak:

* A Térkép nézet a virtuális gép paneljéről, illetve a Azure Monitor alatt Azure Monitor for VMs érhető el.
* A térképen lévő kapcsolatok most már rákattintanak, és megjelenítik a kapcsolat metrikájának adatait a kijelölt kapcsolat oldalsó paneljén.
* Létezik egy új API, amely a térképek létrehozásához használható összetettebb térképek jobb támogatásához.
* A figyelt virtuális gépek már szerepelnek az ügyféloldali csoport csomópontban, és a fánk diagramon látható a csoportban lévő figyelt vs nem figyelt virtuális gépek aránya.  A csoport kibontásakor a számítógépek listájának szűrésére is használható.
* A figyelt virtuális gépek már szerepelnek a kiszolgáló portjának csomópontjaiban, és a fánk diagramon a figyelt és a nem figyelt gépek aránya látható a csoportban.  A csoport kibontásakor a számítógépek listájának szűrésére is használható.
* A Térkép stílusa úgy lett frissítve, hogy jobban konzisztens legyen az Application-adatokból származó app Map szolgáltatással.
* Az oldalsó panelek frissítve lettek, és nem rendelkeznek a Service Map-Update Management, a Change Tracking, a Security és a Service Desk által támogatott teljes integrációs készlettel. 
* A csoportok és gépek leképezésre való kiválasztásának lehetősége frissítve lett, és mostantól támogatja az előfizetéseket, az erőforráscsoportok, az Azure virtuálisgép-méretezési csoportokat és a Cloud Services szolgáltatást.
* Az új Service Map számítógépcsoportok nem hozhatók létre a Azure Monitor for VMs Map szolgáltatásban.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Miért mutatnak pontozott vonalakat a teljesítmény-diagramok?
Ez néhány ok miatt fordulhat elő.  Abban az esetben, ha az adatgyűjtésben hézag van, a vonalakat pontozott értékre ábrázoljuk.  Ha módosította az adatmintavételezés gyakoriságát az engedélyezett teljesítményszámlálók esetében (az alapértelmezett beállítás az adatok gyűjtése 60 másodpercenként), a diagramon szaggatott vonalakat láthat, ha a diagramhoz a keskeny időtartományt választja, a mintavételi gyakoriság pedig kisebb, mint a diagramon használt gyűjtő mérete (például a mintavételezés gyakorisága 10 percenként, a diagram minden gyűjtője pedig 5 perc).  Ha szélesebb időtartományt szeretne megtekinteni, akkor a diagram sorai nem pontokként, hanem folytonos vonalakként jelennek meg.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Támogatottak-e a csoportok Azure Monitor for VMs?
Igen, a függőségi ügynök telepítése után adatokat gyűjtünk a virtuális gépekről az előfizetés, az erőforráscsoport, a virtuálisgép-méretezési csoportok és a Cloud Services alapján.  Ha már használta a Service Map és létrehozott számítógép-csoportokat, ezek is megjelennek.  A számítógépcsoportok akkor is megjelennek a csoportok szűrőben, ha létrehozta őket a megtekintett munkaterülethez. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hogyan tekintse meg a 95. percentilis-sor az összesített teljesítményű diagramokon való vezetésének részleteit?
Alapértelmezés szerint a lista úgy van rendezve, hogy megjelenítse azokat a virtuális gépeket, amelyek a 95. percentilis legmagasabb értékkel rendelkeznek a kiválasztott metrika esetében, kivéve a rendelkezésre álló memória diagramot, amely az 5. percentilis legalacsonyabb értékkel rendelkező gépeket jeleníti meg.  A diagramra kattintva megnyílik a **legfelső N listanézet**  nézet a megfelelő metrika kiválasztásával.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Hogyan kezeli a Map szolgáltatás a duplikált IP-címeket különböző virtuális hálózatok és alhálózatokon?
Ha az IP-tartományokat virtuális gépek vagy Azure virtuálisgép-méretezési csoportok között duplikálja az alhálózatok és a virtuális hálózatok között, akkor Azure Monitor for VMs leképezés helytelen információk megjelenítésére vezethet. Ez egy ismert probléma, amely a tapasztalatok fejlesztését vizsgálja.

### <a name="does-map-feature-support-ipv6"></a>Támogatja az IPv6 a Map funkciót?
A Térkép funkció jelenleg csak az IPv4-t támogatja, és az IPv6 támogatását vizsgáljuk. Az IPv6-on belül bújtatott IPv4-t is támogatja.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Egy erőforráscsoport vagy más nagyméretű csoport térképének betöltésekor nehéz megtekinteni a térképet
Habár a nagy és összetett konfigurációk kezelésére tettük elérhetővé a térképet, rájövünk, hogy a térképen számos csomópont, kapcsolat és csomópont működik fürtként.  Elkötelezettek vagyunk a skálázhatóság növelésének támogatásával.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Miért különbözik a hálózati diagram a teljesítmény lapon, mint a hálózati diagram az Azure-beli virtuális gépek áttekintő oldalán?

Az Azure-beli virtuális gépek áttekintő lapja diagramokat jelenít meg a vendég virtuális gép tevékenységének mérése alapján.  Az Azure-beli virtuális gépek áttekintő hálózati diagramja csak a számlázásra kerülő hálózati forgalmat jeleníti meg.  Ez nem tartalmazza a virtuális hálózatok közötti forgalmat.  A Azure Monitor for VMs számára megjelenített adatok és diagramok a vendég virtuális gépről származó adatokon alapulnak, és a hálózati diagram megjeleníti az összes bejövő és kimenő TCP/IP-forgalmat, beleértve a virtuális gépek közötti hálózatot is.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Hogyan mérjük a válaszidő a VMConnection tárolt és a kapcsolatok paneljén és a munkafüzetek között?

A válaszidő egy közelítés. Mivel nem használjuk az alkalmazás kódját, nem igazán tudjuk, hogy mikor kezdődik a kérelem, és mikor érkezik a válasz. Ehelyett megfigyeljük, hogy a rendszer egy kapcsolatban küldi az adatküldést, majd az adott kapcsolatban visszaérkező adatforrásokat. Az ügynök nyomon követi ezeket a küldési és fogadási kísérleteket, és párosítja őket: az elküldött sorok sorozata, amelyet a fogadások sorozata, a kérelem/válasz pároknak kell értelmezni. A műveletek közötti időzítés a válaszidő. Ez magában foglalja a hálózati késést és a kiszolgáló feldolgozási idejét is.

Ez a közelítés a kérelmek/válaszok alapjául szolgáló protokollok esetében jól működik: egyetlen kérelem érkezik a hálózatra, és egyetlen válasz érkezik. Ez a HTTP (S) esetén (csővezeték nélkül), de más protokollok esetében nem teljesül.

### <a name="are-there-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Vannak korlátozások, ha a Log Analytics ingyenes díjszabási csomagot használom?
Ha az *ingyenes* díjszabási csomaggal konfigurálta a Azure monitort egy log Analytics munkaterülettel, Azure monitor for VMS a Térkép funkció csak a munkaterülethez csatlakozó öt csatlakoztatott gépet fogja támogatni. Ha az ingyenes munkaterülethez öt virtuális gép csatlakozik, az egyik virtuális gép leválasztását követően később egy új virtuális gépet csatlakoztat, az új virtuális gép nem lesz figyelve, és a Térkép oldalon jelenik meg.  

Ebben az esetben a virtuális gép megnyitásakor a **kipróbálás most** lehetőséggel fog megjelenni, és a bal oldali ablaktáblában kiválaszthatja a **bepillantást** , még azután is, hogy már telepítve van a virtuális gépen.  Azonban nem kell megadnia a beállításokat, mivel ez általában akkor fordul elő, ha a virtuális gép nem lett előkészítve a Azure Monitor for VMs. 


## <a name="next-steps"></a>Következő lépések
Ha a kérdés itt nem válaszol, további kérdéseit és válaszait a következő fórumokon tekintheti meg.

- [Naplóelemzés](/answers/topics/azure-monitor.html)
- [Application Insights](/answers/topics/azure-monitor.html)

Ha Azure Monitor általános visszajelzést szeretne kapni, látogasson el a [visszajelzési fórumra](https://feedback.azure.com/forums/34192--general-feedback).
