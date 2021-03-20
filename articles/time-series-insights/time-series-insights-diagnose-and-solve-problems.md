---
title: Problémák diagnosztizálása, hibaelhárítása és megoldása – Azure Time Series Insights
description: Ez a cikk a Azure Time Series Insights-környezetében előforduló gyakori problémák diagnosztizálását, hibaelhárítását és megoldását ismerteti.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 15f2ff5aaa1d731c13125d0a3ab4ac32acb9276c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95023275"
---
# <a name="diagnose-and-solve-issues-in-your-azure-time-series-insights-gen1-environment"></a>A Azure Time Series Insights Gen1-környezet problémáinak diagnosztizálása és megoldása

> [!CAUTION]
> Ez egy Gen1-cikk.

Ez a cikk a Azure Time Series Insights-környezetben felmerülő problémákat ismerteti. A cikk a megoldás lehetséges okait és megoldásait kínálja.

## <a name="video"></a>Videó

### <a name="learn-about-common-azure-time-series-insights-challenges-and-mitigationsbr"></a>Ismerkedjen meg a gyakori Azure Time Series Insights kihívásokkal és megoldásokkal</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Probléma: nincs megjeleníthető érték

Ha a [Azure Time Series Insights Explorerben](https://insights.timeseries.azure.com)nem jelenik meg az adatai, vegye figyelembe a gyakori okokat.

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>A ok: az eseményforrás-adatforrások nem JSON formátumúak

A Azure Time Series Insights csak a JSON-fájlokat támogatja. JSON-minták esetében lásd: [támogatott JSON-alakzatok](./concepts-json-flattening-escaping-rules.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>B ok: az eseményforrás kulcsa hiányzik egy szükséges engedély

* Az Azure IoT Hub IoT hub-ban meg kell adnia a szolgáltatás-csatlakozási engedélyekkel rendelkező kulcsot. Válassza ki a **iothubowner** vagy a **szolgáltatási** házirendet. Mindkettőnek van Service-csatlakozási engedélye.

   [![IoT Hub szolgáltatás csatlakozási engedélyei](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Az Azure Event Hubsban található Event hub esetében meg kell adnia a figyelési engedélyekkel rendelkező kulcsot. Az **olvasási** és **kezelési** házirendek mind a működésük, mind a figyelési engedélyekkel együtt működnek.

   [![Event hub – figyelési engedélyek](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-provided-consumer-group-isnt-exclusive-to-azure-time-series-insights"></a>C ok: a megadott fogyasztói csoport nem kizárólag Azure Time Series Insights

IoT hub vagy Event hub regisztrálása esetén fontos beállítani az adatolvasáshoz használni kívánt fogyasztói csoportot. Ez a fogyasztói csoport *nem* osztható meg. Ha a fogyasztói csoport meg van osztva, a mögöttes IoT hub vagy Event hub automatikusan és véletlenszerűen bontja le az egyik olvasót. Adjon meg egy egyedi fogyasztói csoportot, amelyből a Azure Time Series Insights olvasható.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>D ok: a környezet üzembe helyezése megtörtént

Az adat a környezet és az adatai első létrehozása után néhány percen belül megjelenik a Azure Time Series Insights Explorerben.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Probléma: bizonyos adatértékek megjelennek, de hiányoznak az adathalmazok.

Ha az adathalmazok csak részben jelennek meg, és az adatvesztés látszik, vegye figyelembe ezeket a lehetséges problémákat.

### <a name="cause-a-your-environment-is-being-throttled"></a>A ok: a környezet szabályozása folyamatban van

A [szabályozás](time-series-insights-environment-mitigate-latency.md) gyakori probléma, ha a környezetek kiépítését az adatforrást tartalmazó eseményforrás létrehozása után kell kiépíteni. Az Azure IoT Hub és az Azure Events hubok akár hét napig tárolják az adattárolást. Azure Time Series Insights mindig az eseményforrás legrégebbi eseményével kezdődik (első, első vagy *FIFO*).

5 000 000 ha például egy S1-hez, egy egység Azure Time Series Insights környezethez csatlakozik, akkor a Azure Time Series Insights naponta körülbelül 1 000 000 eseményt olvas be. Előfordulhat, hogy a Azure Time Series Insights öt nap késést tapasztal. De mi történik, a környezet szabályozása folyamatban van.

Ha a régi események szerepelnek az eseményforrásban, kétféleképpen is megközelítheti a szabályozást:

* Módosítsa az eseményforrás megőrzési korlátait, hogy eltávolítsa a régi, Azure Time Series Insightsban nem megjeleníteni kívánt eseményeket.
* A régi események átviteli sebességének növeléséhez hozzon létre nagyobb méretű környezeti méretet (egységek száma). Az előző példában, ha ugyanazon S1-környezetet egy nap alatt öt egységre emeli, a környezetnek egy napon belül kell megjelennie. Ha az állandó állapotú események termelése naponta 1 000 000 vagy kevesebb eseményt használ, csökkentheti az esemény kapacitását egy egységre Azure Time Series Insights a fogások után.

A kényszerített szabályozási korlát a környezet SKU-típusán és kapacitásán alapul. A környezetben lévő összes eseményforrás megosztja ezt a kapacitást. Ha az IoT hub vagy az Event hub eseményforrás a kényszerített határértékeken felül küldi az adatforrást, a szabályozás és a késés is megtapasztalható.

Az alábbi ábrán egy olyan Azure Time Series Insights-környezet látható, amely egy S1-es SKU-t és 3 kapacitást tartalmaz. Naponta 3 000 000 eseményt tud beáramlani.

[![Környezeti kapacitás](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Képzeljen el egy olyan környezetet, amely egy Event hub üzeneteit tölti be. Napi bejövő forgalom aránya körülbelül 67 000 üzenet. Ez a sebesség percenként körülbelül 46 üzenetet fordít.

* Ha az Event hub minden üzenete egyetlen Azure Time Series Insightsi eseményre van lelapulva, a szabályozás nem történik meg.
* Ha az Event hub összes üzenete 100 Azure Time Series Insights eseményre van lelapulva, a 4 600-es eseményeket percenként kell bevezetni.

A 3 kapacitású S1 SKU-környezetek percenként csak 2 100 eseményt tudnak beáramlani (naponta 1 000 000 esemény = 700 esemény percenként három egységben = 2 100 esemény percenként).

Ha szeretné megismerni, hogyan működik az összevonási logika, tekintse meg a [támogatott JSON-alakzatokat](./concepts-json-flattening-escaping-rules.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Ajánlott megoldások a túlzott szabályozáshoz

A késés kijavításához növelje a környezet SKU-kapacitását. További információért olvassa el [a Azure Time Series Insights-környezet skálázása](time-series-insights-how-to-scale-your-environment.md)című témakört.

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>B ok: a korábbi adatmennyiség kezdeti betöltése lassú a bejövő forgalomtól

Meglévő eseményforrás összekapcsolásakor valószínű, hogy az IoT hub vagy az Event hub már tartalmaz adatforrást. A környezet elkezdi az adatok áthúzását az eseményforrás üzenetének megőrzési időszakának elejétől. Ez az alapértelmezett feldolgozás nem bírálható felül. A szabályozást is elvégezheti. A szabályozás hosszabb időt is igénybe vehet, miközben betölti a korábbi adatmennyiséget.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Ajánlott megoldások a nagyméretű kezdeti betöltéshez

A késés kijavítása:

1. Növelje az SKU kapacitását a maximálisan megengedett értékre (ebben az esetben 10). A kapacitás növelését követően a beáramló folyamat sokkal gyorsabban fog megjelenni. A megnövekedett kapacitásért díjat számítunk fel. A rendelkezésre állási diagram megjelenítéséhez a [Azure Time Series Insights Explorerben](https://insights.timeseries.azure.com)tekintheti meg a rendelkezésre állási diagramot.

2. Ha a késés bekövetkezik, csökkentse az SKU kapacitását a normál bejövő forgalom arányára.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Probléma: az adatmegjelenítés korábban megtörtént, de már nem jelenik meg

Ha Azure Time Series Insights már nem tölt be adatfeldolgozást, de az események továbbra is az IOT hub vagy az Event hub szolgáltatásba kerülnek, vegye figyelembe ezt a lehetséges okot.

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-to-be-updated"></a>A ok: a hub-hozzáférési kulcs újralétrehozása megtörtént, és a környezetet frissíteni kell

Ez a probléma akkor fordul elő, ha az eseményforrás létrehozásakor megadott kulcs már nem érvényes. Látni fogja a telemetria a központban, de nem érkezett bejövő üzenetek a Azure Time Series Insightsban. Ha nem biztos abban, hogy a kulcs újra lett-e létrehozva, az Event hub tevékenységi naplójában a "névtér-engedélyezési szabályok létrehozása vagy frissítése" kifejezésre kereshet. Az IoT hub esetében keressen rá a "IotHub-erőforrás létrehozása vagy frissítése" kifejezésre.

Ha az új kulccsal szeretné frissíteni a Azure Time Series Insights környezetet, nyissa meg a hub-erőforrást a Azure Portal, és másolja az új kulcsot. Lépjen a Azure Time Series Insights erőforráshoz, és válassza az **eseményforrás**:

   [![Eseményforrás kiválasztása](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Válassza ki azt az eseményforrás vagy forrást, amelyből a betöltés leállt, illessze be az új kulcsot, majd válassza a **Mentés** lehetőséget:

   [![Beillesztés az új kulcsba](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-the-event-sources-timestamp-property-name-setting-doesnt-work"></a>Probléma: az eseményforrás időbélyeg-tulajdonságának neve beállítás nem működik

Győződjön meg arról, hogy az eseményforrás egy JSON-karakterláncként kapott timestamp tulajdonságának értéke _éééé-hh-NNTóó: PP: mm. FFFFFFFK formátumban_. Íme egy példa: **2008-04-12T12:53Z**.

Ne feledje, hogy az időbélyeg-tulajdonság neve megkülönbözteti a kis-és nagybetűket.

Az időbélyeg-tulajdonságnév rögzítésének és megfelelő működésének legegyszerűbb módja a Azure Time Series Insights Explorer használata. A Azure Time Series Insights Explorerben a diagramot használva válasszon ki egy időszakot, miután megadta az időbélyeg-tulajdonság nevét. Kattintson a jobb gombbal a kijelölésre, majd válassza az **események feltárása** lehetőséget.

Az első oszlop fejlécének az időbélyeg-tulajdonságnév kell lennie. A Word **időbélyeg** mellett **($TS)** jelenik meg.

A következő értékek nem jelennek meg:

* *(ABC)*: azt jelzi, hogy Azure Time Series Insights karakterláncként olvassa be az adatértékeket.
* *Naptár ikon*: azt jelzi, hogy Azure Time Series Insights az adatértékeket datetime értékként olvassa.
* *#*: Azt jelzi, hogy Azure Time Series Insights az adatértékek egész számként való olvasására szolgál.

## <a name="next-steps"></a>Következő lépések

* Olvassa el a [Azure Time Series Insights késésének enyhítését](time-series-insights-environment-mitigate-latency.md)ismertető témakört.

* Ismerje meg [, hogyan méretezheti Azure Time Series Insights-környezetét](time-series-insights-how-to-scale-your-environment.md).