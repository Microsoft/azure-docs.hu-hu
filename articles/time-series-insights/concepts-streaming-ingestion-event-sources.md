---
title: Adatfolyam-betöltési események forrásai – Azure Time Series Insights Gen2 | Microsoft Docs
description: Ismerkedjen meg Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 4e22d93d3037c190193f53b7cfdbc87cff2da6ed
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504396"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure Time Series Insights Gen2-esemény forrásai

Az Azure Time Series Insights Gen2-környezet akár két folyamatos átviteli erőforrással is rendelkezhet. Az Azure-erőforrások két típusa támogatott bemenetként:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Az eseményeket UTF-8 kódolású JSON-ként kell elküldeni.

## <a name="create-or-edit-event-sources"></a>Eseményforrás létrehozása vagy szerkesztése

Az eseményforrás a hub és a Azure Time Series Insights Gen2-környezet közötti kapcsolat, és az erőforráscsoport egy külön típusú erőforrást `Time Series Insights event source` hoz létre. A IoT Hub vagy az Event hub-erőforrás (ok) ugyanabban az Azure-előfizetésben használható, mint a Azure Time Series Insights Gen2-környezet vagy egy másik előfizetés. Azonban ajánlott a Azure Time Series Insights-környezet és a IoT Hub vagy az Event hub egyazon Azure-régión belüli bekapcsolódása.

Használhatja a [Azure Portalt](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment), az [Azure CLI](https://docs.microsoft.com/cli/azure/ext/timeseriesinsights/tsi/event-source)-t, a [Azure Resource Manager sablonokat](time-series-insights-manage-resources-using-azure-resource-manager-template.md), valamint a [REST API](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) a környezete eseményforrás létrehozásához, szerkesztéséhez vagy eltávolításához.

## <a name="start-options"></a>Indítási beállítások

Egy eseményforrás létrehozásakor lehetősége van megadnia, hogy milyen előre meglévő adatokat kell gyűjteni. Ez a beállítás nem kötelező. A következő lehetőségek érhetők el:

| Név   |  Leírás  |  Példa Azure Resource Manager sablonra |
|----------|-------------|------|
| EarliestAvailable | A IoT vagy az Event hub-ban tárolt összes már meglévő adatot betöltheti | `"ingressStartAt": {"type": "EarliestAvailable"}` |
| EventSourceCreationTime |  Az eseményforrás létrehozása után megjelenő adatmennyiség megkezdése. A rendszer figyelmen kívül hagyja az eseményforrás létrehozása előtt továbbított összes korábbi adatmennyiséget. Ez az alapértelmezett beállítás a Azure Portal   |   `"ingressStartAt": {"type": "EventSourceCreationTime"}` |
| CustomEnqueuedTime | Az Ön környezete az egyéni várólistán lévő idő (UTC) alapján tölti le az adatait. Minden olyan esemény betöltése és tárolása történik, amely az egyéni várólistán lévő idején vagy után várólistán lévő be a IoT vagy az Event hub-ba. A rendszer figyelmen kívül hagyja az egyéni várólistán lévő idő előtti összes eseményt. Vegye figyelembe, hogy a "várólistán lévő Time" kifejezés arra az időre vonatkozik (UTC szerint), amelyet az esemény a IoT vagy az Event hub esetében megérkezett. Ez eltér az esemény törzsében található egyéni [timestamp tulajdonságtól](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp) . |     `"ingressStartAt": {"type": "CustomEnqueuedTime", "time": "2021-03-01T17:00:00.20Z"}` |

> [!IMPORTANT]
>
> - Ha a EarliestAvailable lehetőséget választja, és sok már meglévő adattal rendelkezik, akkor magas kezdeti késést tapasztalhat, mivel az Azure Time Series Insights Gen2-környezet az összes adatait feldolgozza.
> - Ezt a nagy késést végül az adat indexelésének megfelelően kell ellátni. Ha folyamatos, magas késést tapasztal, küldjön támogatási jegyet a Azure Portalon keresztül.

* EarliestAvailable

![EarliestAvailable diagram](media/concepts-streaming-event-sources/event-source-earliest-available.png)

* EventSourceCreationTime

![EventSourceCreationTime diagram](media/concepts-streaming-event-sources/event-source-creation-time.png)

* CustomEnqueuedTime

![CustomEnqueuedTime diagram](media/concepts-streaming-event-sources/event-source-custom-enqueued-time.png)


## <a name="streaming-ingestion-best-practices"></a>Az adatfolyamok betöltésének ajánlott eljárásai

- Mindig hozzon létre egy egyedi fogyasztói csoportot a Azure Time Series Insights Gen2-környezet számára az eseményforrás adatainak felhasználásához. A fogyasztói csoportok újbóli használata véletlenszerű leválasztást eredményezhet, ami adatvesztést eredményezhet.

- Konfigurálja Azure Time Series Insights Gen2-környezetét és a IoT Hub és/vagy Event Hubs ugyanabban az Azure-régióban. Bár az eseményforrás egy különálló régióban is konfigurálható, ez a forgatókönyv nem támogatott, és nem garantálható a magas rendelkezésre állás.

- Ne lépje túl a környezet [átviteli sebességének korlátját](./concepts-streaming-ingress-throughput-limits.md) vagy a partíciós korlátot.

- A késési [riasztások](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) beállításával értesítést kaphat, ha a környezete problémákat tapasztal az adatfeldolgozás során. A javasolt riasztási feltételekhez lásd az alábbi [éles számítási feladatokat](./concepts-streaming-ingestion-event-sources.md#production-workloads) .

- A streaming betöltést csak a közel valós idejű és a legutóbbi adatmennyiségek esetében használja, a folyamatos adatátvitelek nem támogatottak.

- Ismerje meg, hogy a rendszer hogyan fogja kikerülni a tulajdonságokat és a JSON [-adatgyűjtést és-tárolást.](./concepts-json-flattening-escaping-rules.md)

- Az eseményforrás-kapcsolati karakterláncok megadásakor kövesse a legalacsonyabb jogosultsági szint elvét. Event Hubs esetében csak a *küldési* jogcímet konfigurálja, és a IoT hub csak a *szolgáltatás csatlakozási* engedélyét használja.

> [!CAUTION]
> Ha törli a IoT Hub vagy az Event hub-t, és újra létrehoz egy új erőforrást ugyanazzal a névvel, létre kell hoznia egy új eseményforrás, és csatolnia kell az új IoT Hub vagy az Event hub-t. A rendszer addig nem tölti be az adatot, amíg el nem végzi ezt a lépést.

## <a name="production-workloads"></a>Éles számítási feladatok

A fenti ajánlott eljárások mellett azt javasoljuk, hogy az üzleti szempontból kritikus fontosságú számítási feladatokhoz a következőket alkalmazza.

- Növelje IoT Hub vagy az Event hub adatmegőrzési idejét legfeljebb hét napig.

- Hozzon létre környezeti riasztásokat a Azure Portal. A platform [metrikái](./how-to-monitor-tsi-reference.md#metrics) alapján történő riasztások lehetővé teszik a végpontok közötti folyamat működésének ellenőrzését. A riasztások létrehozásával és kezelésével kapcsolatos utasítások [itt](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts)találhatók. Javasolt riasztási feltételek:

  - A IngressReceivedMessagesTimeLag nagyobb, mint 5 perc
  - A IngressReceivedBytes 0
- Tartsa a betöltési terhelést az IoT Hub vagy az Event hub-partíciók között.

### <a name="historical-data-ingestion"></a>Korábbi adatfeldolgozás

Azure Time Series Insights Gen2 jelenleg nem támogatja az adatfolyam-továbbítási folyamat használatát a korábbi adatimportáláshoz. Ha a korábbi adatait importálnia kell a környezetbe, kövesse az alábbi irányelveket:

- Ne továbbítsa párhuzamosan az élő és a korábbi adatforrásokat. A lekéréses adatmennyiség miatt a lekérdezés teljesítménye csökken.
- A legjobb teljesítmény érdekében időben berendezheti a múltbeli adatmennyiséget.
- Maradjon az alábbi betöltési átviteli sebességre vonatkozó korlátok között.
- Ha az adatok régebbiek, mint a meleg tárolási megőrzési időszak, tiltsa le a meleg tárolást.

## <a name="event-source-timestamp"></a>Eseményforrás időbélyege

Az eseményforrás konfigurálásakor a rendszer megkéri, hogy adjon meg egy időbélyeg-azonosító tulajdonságot. A timestamp tulajdonság az események időbeli nyomon követésére szolgál. Ez az idő lesz a lekérdezési API-k időbélyegként való használata, `$ts` valamint a Azure Time Series Insights Explorerben ábrázolt adatsorozatok. [](/rest/api/time-series-insights/dataaccessgen2/query/execute) Ha nem ad meg tulajdonságot a létrehozási időben, vagy ha egy eseményből hiányzik az időbélyegző tulajdonság, akkor az esemény IoT Hub vagy Events hub-várólistán lévő ideje lesz alapértelmezettként használva. Az időbélyegző-tulajdonságok értékeit a rendszer UTC szerint tárolja.

Általánosságban elmondható, hogy a felhasználók testreszabják az időbélyegző tulajdonságot, és azt az időpontot használják, amikor az érzékelő vagy a címke az olvasást az alapértelmezett hub-várólistán lévő idejének használata helyett használja. Ez különösen akkor szükséges, ha az eszközök időszakos kapcsolati adatvesztéssel rendelkeznek, és a késleltetett üzenetek kötegét továbbítják Azure Time Series Insights Gen2.

Ha az egyéni időbélyeg egy beágyazott JSON-objektumon vagy egy tömbön belül van, meg kell adnia a megfelelő tulajdonságnév-nevet az [összeolvasztási és Escape-elnevezési konvenciók](concepts-json-flattening-escaping-rules.md)követése után. Például az [itt](concepts-json-flattening-escaping-rules.md#example-a) látható JSON-adattartalomhoz tartozó eseményforrás időbélyegét kell megadni `"values.time"` .

### <a name="time-zone-offsets"></a>Időzóna-eltolások

Az időbélyegeket ISO 8601 formátumban kell elküldeni, és az UTC szerint lesz tárolva. Ha egy időzóna-eltolás van megadva, a rendszer alkalmazza az eltolást, majd az UTC formátumban tárolt és visszaadott időt. Ha az eltolás formátuma nem megfelelő, a rendszer figyelmen kívül hagyja. Olyan helyzetekben, ahol a megoldás esetleg nem rendelkezik az eredeti eltolás kontextusával, elküldheti az eltolási adatait egy további külön esemény-tulajdonságban, így biztosítva, hogy megmaradjon, és az alkalmazás hivatkozhat egy lekérdezési válaszra.

Az időzóna-eltolást a következők egyikének kell megformáznia:

± HHMMZ</br>
± HH: PP</br>
± HH: MMZ</br>

## <a name="next-steps"></a>Következő lépések

- Olvassa el a [JSON-összeolvasztási és-Escape-szabályokat](./concepts-json-flattening-escaping-rules.md) , hogy megtudja, hogyan lesznek tárolva az események.

- A környezet [adatátviteli korlátainak](./concepts-streaming-ingress-throughput-limits.md) megismerése
