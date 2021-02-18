---
title: Azure Monitor adatplatform | Microsoft Docs
description: A Azure Monitor által gyűjtött figyelési adatok elkülönül a könnyű és a közel valós idejű forgatókönyvek és a speciális elemzéshez használt naplók támogatásához szükséges mérőszámokkal.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: bccd1f001cca67a5be3d14c50e6a6db96cb6584c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100615285"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor adatplatform

A napjainkban a Felhőbeli és a helyszíni szolgáltatásokra támaszkodó elosztott alkalmazásokat futtató összetett számítástechnikai környezetek megfigyelésének engedélyezése megköveteli az operatív adatok gyűjtését minden rétegből és az elosztott rendszer minden összetevőjéből. Részletes elemzéseket kell végeznie az adatokról, és összevonhatja egyetlen, különböző perspektívákat tartalmazó ablaktáblába, hogy támogassa a szervezete számos résztvevőjét.

A [Azure monitor](overview.md) különböző forrásokból származó adatokat gyűjt és összesít egy közös adatplatformba, ahol elemzésre, vizualizációra és riasztásra is használható. Egységes felhasználói élményt nyújt több forrásból származó adatokon, ami részletes elemzéseket nyújt az összes figyelt erőforrásról, akár más olyan szolgáltatásokból származó adatokból, amelyek az adataikat a Azure Monitor tárolják.


![Azure Monitor – áttekintés](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Megfigyelt adatszolgáltatások Azure Monitor
A mérőszámokat, a naplókat és az elosztott nyomkövetéseket általában a megfigyelés három pillérének nevezzük. Ezek a különböző típusú adatok, amelyeket a figyelő eszköznek össze kell gyűjtenie, és elemezni kell, hogy elegendő legyen a figyelt rendszer megfelelő megfigyelése. A megfigyelés úgy érhető el, ha több pillérből származó adatokkal korrelál, és az adatok összesítése a figyelt erőforrások teljes készlete között történik. Mivel a Azure Monitor különböző forrásokból származó adatok tárolására szolgálnak, az adatok összekapcsolhatók és elemezhetők a közös eszközkészletek használatával. Emellett a több Azure-előfizetésre és-bérlőre vonatkozó adatokkal is összefügg, valamint más szolgáltatásokra vonatkozó adatok üzemeltetése mellett.

Az Azure-erőforrások jelentős mennyiségű figyelési adattal járnak. Azure Monitor összevonja ezeket az adatokat más forrásokból származó figyelési adatokkal a metrikák vagy naplók platformba. Mindegyiket bizonyos figyelési helyzetekre optimalizáltuk, és mindegyik támogatja a Azure Monitor különböző funkcióit. Az olyan funkciók, mint például az adatelemzés, a vizualizációk vagy a riasztások, meg kell ismerniük a különbségeket, hogy a lehető leghatékonyabb és költséghatékony módon lehessen megvalósítani a szükséges helyzetet. A Azure Monitor például [Application Insights](app/app-insights-overview.md) vagy [Azure monitor for VMS](vm/vminsights-overview.md) elemzési eszközökkel rendelkezik, amelyek lehetővé teszik, hogy az adott figyelési forgatókönyvre koncentráljon anélkül, hogy meg kellene ismernie a két adattípus közötti különbséget. 


### <a name="metrics"></a>Mérőszámok
A [metrikák](essentials/data-platform-metrics.md) olyan numerikus értékek, amelyek egy adott rendszer bizonyos aspektusait írják le egy adott időpontban. Rendszeres időközönként vannak gyűjtve, időbélyeggel, névvel, értékkel és egy vagy több definiáló címkével azonosítva. A metrikák sokféle algoritmussal összesíthetők, összehasonlíthatók más metrikákkal, és elemezhetők az időbeli trendjeik. 

A Azure Monitor metrikáit egy idősorozat-adatbázisban tárolják, amely az időbélyegzővel ellátott adatok elemzésére van optimalizálva. Ez a mérőszámok különösen a riasztások és a problémák gyors észlelésére alkalmasak. Megtudhatják, hogyan végzi a rendszer működését, de általában a naplók használatával kell összekapcsolni a problémák kiváltó okát.

A metrikák interaktív elemzéshez érhetők el az [Azure Metrikaböngésző](essentials/metrics-getting-started.md)Azure Portal. Hozzáadhatók egy [Azure-irányítópulthoz](app/tutorial-app-dashboards.md) a vizualizációhoz, más adattal együtt, és közel valós idejű [riasztásokhoz](alerts/alerts-metric.md).

További információ a Azure Monitor mérőszámokról, beleértve a [Azure monitor metrikájában](essentials/data-platform-metrics.md)lévő adatforrásokat.

### <a name="logs"></a>Naplók
A [naplók](logs/data-platform-logs.md) a rendszeren belül bekövetkezett események. Különböző típusú adattípusokat tartalmazhatnak, és lehetnek strukturált vagy szabad formátumú szövegek időbélyeg használatával. Szórványosan állhatnak elő, amikor a környezetbeli események naplóbejegyzéseket generálnak, és egy erős terhelésű rendszer általában nagyobb naplókat állít elő.

A Azure Monitor lévő naplókat az [Azure Adatkezelőon](/azure/data-explorer/) alapuló log Analytics munkaterületen tároljuk, amely hatékony elemzési motort és [gazdag lekérdezési nyelvet](/azure/kusto/query/)biztosít. A naplók általában elegendő információt biztosítanak az azonosított probléma teljes kontextusának biztosításához, és értékesek a problémák fő eseteinek azonosításához.

> [!NOTE]
> Fontos különbséget tenni Azure Monitor naplók és a naplózási adatforrások között az Azure-ban. Az Azure-beli előfizetési szintű események például egy, a Azure Monitor menüből megtekinthető [tevékenységi naplóba](essentials/platform-logs-overview.md) íródnak. A legtöbb erőforrás operatív adatokat fog írni egy olyan [erőforrás-naplóba](essentials/platform-logs-overview.md) , amelyet továbbíthat a különböző helyszínekhez. Azure Monitor a naplók egy olyan naplózási adatplatform, amely a tevékenységek naplóit és erőforrás-naplóit, valamint más figyelési adatokat gyűjt, így részletes elemzéseket biztosít a teljes erőforrás-készleten belül.


 A [naplózási lekérdezéseket](logs/log-query-overview.md) interaktív módon is használhatja a Azure Portal [log Analyticsével](logs/log-query-overview.md) , vagy az eredményeket hozzáadhat egy [Azure-irányítópulthoz](app/tutorial-app-dashboards.md) a vizualizációhoz más adattal kombinálva. Létrehozhat olyan [naplózási riasztásokat](alerts/alerts-log.md) is, amelyek riasztást küldenek egy ütemezett lekérdezés eredményei alapján.

További információ az Azure Monitor naplókról, beleértve az adatforrásokat [Azure monitor naplókban](logs/data-platform-logs.md).

### <a name="distributed-traces"></a>Elosztott Nyomkövetések
A nyomkövetés olyan kapcsolódó események sorozata, amelyek egy adott felhasználói kérést követnek egy elosztott rendszeren keresztül. Felhasználhatók az alkalmazás kódjának viselkedésének meghatározására és a különböző tranzakciók teljesítményére. Míg a naplókat gyakran egy elosztott rendszer egyes összetevői hozzák létre, a nyomkövetés az alkalmazás működésének és teljesítményének a teljes összetevői között méri.

Az elosztott nyomkövetés a Azure Monitorben engedélyezve van a [Application INSIGHTS SDK](app/distributed-tracing.md)-val, és a nyomkövetési adatok tárolása a Application Insights által összegyűjtött más alkalmazás-naplózási adatokkal történik. Így a naplófájlok, az irányítópultok és a riasztások más naplózási adatként is elérhetővé válnak.

További információk az elosztott nyomkövetéssel kapcsolatban: [Mi az elosztott nyomkövetés?](app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Azure Monitor mérőszámok és naplók összehasonlítása

A következő táblázat összehasonlítja Azure Monitor metrikáit és naplóit.

| Attribútum  | Mérőszámok | Naplók |
|:---|:---|:---|
| Előnyök | Könnyű és közel valós idejű forgatókönyvek, például riasztások. Ideális megoldás a problémák gyors észlelésére. | Elemezve gazdag lekérdezési nyelvvel. Ideális a mélyreható elemzéshez és a kiváltó okok azonosításához. |
| Adatok | Csak numerikus értékek | Szöveg-vagy numerikus adat |
| Struktúra | A tulajdonságok szabványos készlete, beleértve a mintavételezési időt, a figyelt erőforrást, a numerikus értéket. Néhány metrika több dimenziót tartalmaz a további definíciók meghatározásához. | A tulajdonságok egyedi készlete a napló típusától függően. |
| Gyűjtemény | Rendszeres időközönként összegyűjtve. | Előfordulhat, hogy a rendszer időnként összegyűjti a létrehozandó rekordot. |
| Megtekintés az Azure Portalon | Metrikaböngésző | Log Analytics |
| Adatforrások közé tartoznak az alábbiak: | Az Azure-erőforrásokból gyűjtött platform-metrikák.<br>Application Insights által figyelt alkalmazások.<br>Az alkalmazás vagy API által definiált egyéni. | Alkalmazás-és erőforrás-naplók.<br>Figyelési megoldások.<br>Ügynökök és virtuálisgép-bővítmények.<br>Alkalmazás-kérelmek és kivételek.<br>Azure Security Center.<br>Adatgyűjtő API. |

## <a name="collect-monitoring-data"></a>Megfigyelési adatok gyűjtése
A [Azure monitor különböző adatforrásai](agents/data-sources.md) log Analytics-munkaterületre (naplók) vagy a Azure monitor metrikák adatbázisára (mérőszámokra) vagy mindkettőre fognak írni. Egyes források közvetlenül ezekbe az adattárakba fognak írni, míg mások más helyre is írhatnak, például az Azure Storage-ba, és némi konfigurációt igényelnek a naplók és a metrikák feltöltéséhez. 

A különböző típusú adatforrások listájának megjelenítéséhez tekintse meg a Azure Monitor és [az Azure monitor naplóiban](logs/data-platform-logs.md) található [metrikákat](essentials/data-platform-metrics.md) .


## <a name="stream-data-to-external-systems"></a>Adatfolyamok továbbítása külső rendszerekre
Amellett, hogy az Azure-ban található eszközöket használja a figyelési adatok elemzéséhez, előfordulhat, hogy egy külső eszközre kell továbbítania, például egy biztonsági információ és egy esemény-felügyeleti (SIEM) termékre. Ez a továbbítás általában közvetlenül a figyelt erőforrásokon keresztül történik az [Azure Event Hubson](../event-hubs/index.yml)keresztül. Néhány forrás úgy konfigurálható, hogy közvetlenül egy Event hubhoz küldje az adatküldést, miközben egy másik folyamat, például egy logikai alkalmazás is használható a szükséges információk lekéréséhez. A részletekért tekintse meg az [Azure monitoring-adatok átvitele egy Event hub-](essentials/stream-monitoring-data-event-hubs.md) ba című témakört.



## <a name="next-steps"></a>Következő lépések

- További információ a [Azure monitor metrikákkal](essentials/data-platform-metrics.md)kapcsolatban.
- További információ a [Azure monitor naplóiról](logs/data-platform-logs.md).
- Ismerje meg az Azure különböző erőforrásaihoz [elérhető figyelési információkat](agents/data-sources.md) .

