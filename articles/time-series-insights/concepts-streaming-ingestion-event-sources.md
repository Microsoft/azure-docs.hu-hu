---
title: Streamelési eseményforrások – Azure Time Series Insights Gen2-| Microsoft Docs
description: Ismerje meg az adatok streamelését Azure Time Series Insights Gen2-be.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: e0d40a4e0e376a42841bd8df5d76e5c83d11b1e3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865481"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure Time Series Insights Gen2-eseményforrások

A Azure Time Series Insights Gen2-környezet legfeljebb két streamelési eseményforrást is lehet. Kétféle Azure-erőforrás támogatott bemenetként:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Az eseményeket UTF-8 kódolású JSON formátumban kell elküldeni.

## <a name="create-or-edit-event-sources"></a>Eseményforrások létrehozása vagy szerkesztése

Az eseményforrás a hub és a Azure Time Series Insights Gen2-környezet közötti kapcsolat, és egy külön típusú erőforrás jön létre az `Time Series Insights event source` erőforráscsoportban. A IoT Hub vagy Event Hub-erőforrás(k) ugyanabban az Azure-előfizetésben is elérhető(k), mint a Azure Time Series Insights Gen2-környezet vagy egy másik előfizetés. Az ajánlott eljárás azonban az, ha a Azure Time Series Insights és a IoT Hub vagy az eseményközpontot ugyanabban az Azure-régióban található.

A környezet [eseményforrásának](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment)létrehozásához, szerkesztéséhez vagy eltávolításához [](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) használhatja a Azure Portal , [az Azure CLI,](/cli/azure/tsi/event-source)Azure Resource Manager [sablonok](time-series-insights-manage-resources-using-azure-resource-manager-template.md)és a REST API sablonokat.

> [!WARNING]
> Ne korlátozza a nyilvános internet-hozzáférést egy központra vagy eseményforrásra, Time Series Insights a szükséges kapcsolat megszakad.

## <a name="start-options"></a>Indítási beállítások

Eseményforrás létrehozásakor megadhatja, hogy milyen, már létező adatokat kell gyűjteni. Ez a beállítás nem kötelező. A következő lehetőségek érhetők el:

| Név   |  Leírás  |  Azure Resource Manager példasablonra |
|----------|-------------|------|
| EarliestAvailable (Legkorábbi rendelkezésre áll) | Az IoT-ben vagy az Eseményközpontban tárolt összes meglévő adat bemenően | `"ingressStartAt": {"type": "EarliestAvailable"}` |
| EventSourceCreationTime |  Kezdje meg az eseményforrás létrehozása után érkező adatok beérkezését. A rendszer figyelmen kívül hagyja az eseményforrás létrehozása előtt streamelt, már meglévő adatokat. Ez az alapértelmezett beállítás a Azure Portal   |   `"ingressStartAt": {"type": "EventSourceCreationTime"}` |
| CustomEnqueuedTime | A környezet az egyéni idősorból (UTC) továbbítja az adatokat. Az IoT-be vagy az eseményközpontba az egyéni be- vagy után beemelt események be lesznek edve és tárolva lesznek. Az egyénileg bekért idő előtt érkezett eseményeket a rendszer figyelmen kívül hagyja. Vegye figyelembe, hogy az "üzenetsorba állítva idő" azt az időt jelenti (UTC-ben), amikor az esemény megérkezett az IoT-be vagy az eseményközpontba. Ez eltér az [](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp) esemény törzsében található egyéni időbélyeg-tulajdonságtól. |     `"ingressStartAt": {"type": "CustomEnqueuedTime", "time": "2021-03-01T17:00:00.20Z"}` |

> [!IMPORTANT]
>
> - Ha az EarliestAvailable (Legkorábbi rendelkezésre álló) lehetőséget választja, és sok már meglévő adat áll rendelkezésre, magas kezdeti késést tapasztalhat, mivel a Azure Time Series Insights Gen2-környezet feldolgozza az összes adatot.
> - A magas késés idővel megszűnik az adatok indexelése után. Küldjön támogatási jegyet a Azure Portal ha folyamatos nagy késést tapasztal.

- EarliestAvailable (Legkorábbi rendelkezésre áll)

![EarliestAvailable Diagram](media/concepts-streaming-event-sources/event-source-earliest-available.png)

- EventSourceCreationTime

![EventSourceCreationTime Diagram](media/concepts-streaming-event-sources/event-source-creation-time.png)

- CustomEnqueuedTime

![CustomEnqueuedTime Diagram](media/concepts-streaming-event-sources/event-source-custom-enqueued-time.png)

## <a name="streaming-ingestion-best-practices"></a>Ajánlott eljárások a streamekbe való streameléshez

- Mindig hozzon létre egy egyedi fogyasztói csoportot a Azure Time Series Insights Gen2-környezethez, hogy felhasználja az eseményforrásból származó adatokat. A fogyasztói csoportok újrahasználása véletlenszerű kapcsolatválasztást okozhat, és adatvesztést eredményezhet.

- Konfigurálja Azure Time Series Insights Gen2-környezetet és a IoT Hub/vagy Event Hubs ugyanabban az Azure-régióban. Bár lehetséges egy eseményforrást külön régióban konfigurálni, ez a forgatókönyv nem támogatott, és nem tudjuk garantálni a magas rendelkezésre állást.

- Ne ússa [](./concepts-streaming-ingress-throughput-limits.md) meg a környezet átviteli sebességének korlátját vagy partíciónkénti korlátját.

- Állítson be késési [riasztást,](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) hogy értesítést kap, ha a környezetben problémákat tapasztal az adatok feldolgozása során. A [javasolt riasztási feltételeket az alábbi](./concepts-streaming-ingestion-event-sources.md#production-workloads) Éles számítási feladatok alatt láthatja.

- Streamelési adatbestreamelés használata csak közel valós idejű és legutóbbi adatokhoz, a streamelési előzményadatok nem támogatottak.

- A tulajdonságok escape-ként való megjelölésének, valamint a JSON-adatok el simításának és [tárolásának a megtekintése.](./concepts-json-flattening-escaping-rules.md)

- Az eseményforrás kapcsolati sztringek biztosításakor kövesse a legkevesebb jogosultság elvének megfelelőt. A Event Hubs konfigurálnia kell egy megosztott  hozzáférési szabályzatot csak a küldési jogcímre, IoT Hub csak a szolgáltatáshoz való csatlakozás *engedélyét* használja.

> [!CAUTION]
> Ha törli a IoT Hub vagy az eseményközpontot, és újra létrehoz egy új erőforrást ugyanazokkal a névvel, létre kell hoznia egy új eseményforrást, és csatolnia kell az új IoT Hub vagy eseményközpontot. Az adatok csak a lépés befejezése után lesznek behozva.

## <a name="production-workloads"></a>Éles számítási feladatok

Javasoljuk, hogy a fenti ajánlott eljárások mellett az alábbiakat is implementálja az üzleti kritikus fontosságú számítási feladatokhoz.

- Növelje a IoT Hub vagy az eseményközpont adatmegőrzési idejét legfeljebb hét napra.

- Hozzon létre környezeti riasztásokat a Azure Portal. A platformmetrikákon [](./how-to-monitor-tsi-reference.md#metrics) alapuló riasztások lehetővé teszik a folyamat végpontok között viselkedésének érvényesítését. A riasztások létrehozására és kezelésére vonatkozó utasítások itt [találhatóak.](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) Javasolt riasztási feltételek:

  - Az IngressReceivedMessagesTimeLag nagyobb, mint 5 perc
  - Az IngressReceivedBytes 0
- A betöltési terhelést kiegyensúlyozottan tarthatja a IoT Hub- vagy Event Hub-partíciók között.

### <a name="historical-data-ingestion"></a>Előzményadatok be- és bebe-

A 2. generációs Azure Time Series Insights a streamelési folyamat használata előzményadatok importálására. Ha múltbeli adatokat kell importálni a környezetbe, kövesse az alábbi irányelveket:

- Ne streamelj élő és előzményadatokat párhuzamosan. A nem sorrendben bemenő adatok a lekérdezések teljesítményének csökkent teljesítményét eredményezik.
- A legjobb teljesítmény érdekében az előzményadatokat idő szerint kell behozni.
- Maradjon az alábbi átviteli sebességkorláton belül.
- Tiltsa le a Warm Store-t, ha az adatok régebbiek, mint a Warm Store megőrzési ideje.

## <a name="event-source-timestamp"></a>Eseményforrás időbélyege

Eseményforrás konfigurálásakor meg kell adnia egy időbélyeg-azonosító tulajdonságot. Az időbélyeg tulajdonság az események nyomon követésére használható az idő alatt. Ez az az idő, amely időbélyegként lesz használva a Lekérdezés API-kban, és a sorozatok ábrázolása a Azure Time Series Insights `$ts` Explorerben. [](/rest/api/time-series-insights/dataaccessgen2/query/execute) Ha a létrehozáskor nem ad meg tulajdonságot, vagy ha az időbélyeg tulajdonság hiányzik egy eseményből, akkor a rendszer alapértelmezés szerint az esemény IoT Hub- vagy Event Hubs-nak megfelelő bequeued idejét használja. Az időbélyegző-tulajdonságértékek tárolása UTC időzónában történt.

A felhasználók általában úgy döntik, hogy testreszabják az időbélyeg-tulajdonságot, és azt az időt használják, amikor az érzékelő vagy címke generálta az olvasást az alapértelmezett központbesorolt idő helyett. Ez különösen akkor szükséges, ha az eszközök időszakos kapcsolatvesztéssel és késleltetett üzenetek kötegének a Gen2-Azure Time Series Insights vannak továbbítva.

Ha az egyéni időbélyeg egy beágyazott JSON-objektumban vagy tömbben található, meg kell adnia a megfelelő tulajdonságnevet az el simítási és a kikerüléselhelyezési elnevezési konvencióink [szerint.](concepts-json-flattening-escaping-rules.md) Az itt látható hasznos JSON-adatok eseményforrás-időbélyegét például a következőként kell megadni: [](concepts-json-flattening-escaping-rules.md#example-a) `"values.time"` .

### <a name="time-zone-offsets"></a>Időzóna-eltolások

Az időbélyegeket ISO 8601 formátumban kell elküldeni, és UTC időzónában lesznek tárolva. Ha meg van adva egy időzóna-eltolódás, a rendszer alkalmazza az eltolást, majd a tárolt és visszaadott időt UTC formátumban. Ha az eltolás nem megfelelően van formázva, a rendszer figyelmen kívül hagyja. Olyan esetekben, amikor a megoldás nem tartalmaz az eredeti eltolás kontextusát, elküldheti az eltolási adatokat egy további külön eseménytulajdonságba annak érdekében, hogy megmaradjon, és hogy az alkalmazás hivatkozni tud a lekérdezési válaszban.

Az időzóna-eltolódást a következő formátumban kell formázni:

±HHMMZ<br />
±HH:MM<br />
±HH:MMZ

## <a name="next-steps"></a>Következő lépések

- Az események tárolására vonatkozó információért olvassa el a [JSON-simítási](./concepts-json-flattening-escaping-rules.md) és -szabálykitörési szabályokat.

- A környezet átviteli [sebességére vonatkozó korlátozások](./concepts-streaming-ingress-throughput-limits.md)
