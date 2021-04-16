---
title: Python-alkalmazások figyelése Azure Monitor | Microsoft Docs
description: Az OpenCensus Python és a Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
ms.openlocfilehash: 92d954a865a2d4a8c55177b132139dcd7d0444ef
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515923"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Az Azure Monitor beállítása a Python-alkalmazáshoz

Azure Monitor támogatja a Python-alkalmazások elosztott nyomkövetését, metrikagyűjtését és naplózását az [OpenCensus integrációja révén.](https://opencensus.io) Ez a cikk végigvezeti az OpenCensus Pythonhoz való beállításának folyamatán, és a monitorozási adatoknak az Azure Monitor.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
- Python-telepítés. Ez a cikk [a Python 3.7.0-s](https://www.python.org/downloads/release/python-370/)verzióját használja, bár más verziók valószínűleg kisebb módosításokkal is működnek. Az SDK csak a Python 2.7-es és 3.4-v3.7-es verzióját támogatja.
- Hozzon létre egy Application Insights [erőforrást.](./create-new-resource.md) A rendszer saját eszközkulcsot (ikey) rendel hozzá az erőforráshoz.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Az OpenCensus Python SDK for Azure Monitor

Telepítse az OpenCensus Azure Monitor a következőt:

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> A `python -m pip install opencensus-ext-azure` parancs feltételezi, hogy a Python-telepítéshez be van állítva `PATH` egy környezeti változó. Ha még nem konfigurálta ezt a változót, meg kell adni a pythonos végrehajtható fájl helyének teljes könyvtárát. Az eredmény egy ehhez hasonló parancs: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure` .

Az SDK három különböző Azure Monitor használ, hogy különböző típusú telemetriai adatokat küldjön a Azure Monitor. Ezek a nyomkövetés, a metrikák és a naplók. Ezekről a telemetriai típusokról az adatplatform áttekintésében [talál további információt.](../data-platform.md) Az alábbi utasításokat követve küldheti el ezeket a telemetriatípusokat a három aknán keresztül.

## <a name="telemetry-type-mappings"></a>Telemetriatípus-leképezések

Az OpenCensus által a 2016-ban látható telemetriatípusokra leképezett a Azure Monitor.

| A megfigyelhetőség alappillére | Telemetria típusa a Azure Monitor    | Magyarázat                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| Naplók                    | Nyomkövetések, kivételek, customEvents   | Naplótelemetelemetia, kivételtelemetelemetia, esemény-telemetria |
| Mérőszámok                 | customMetrics, performanceCounters | Egyéni metrikák teljesítményszámlálói                |
| Nyomkövetés                 | Kérelmek függőségei             | Bejövő kérések, kimenő kérések                |

### <a name="logs"></a>Naplók

1. Először is hozzunk létre néhány helyi naplóadatot.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. A kód folyamatosan kér egy értéket. A rendszer minden bevitt értékhez naplóbejegyzést bocsát ki.

    ```output
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. Bár az értékek megadása bemutató célokra hasznos, végső soron a naplóadatokat a Azure Monitor. Adja át a kapcsolati sztringet közvetlenül a kávénak. Azt is megadhatja egy környezeti változóban( `APPLICATIONINSIGHTS_CONNECTION_STRING` ). Módosítsa az előző lépésben lekért kódot az alábbi kódminta alapján:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)

    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. A gyártó naplóadatokat küld a Azure Monitor. Az adatokat a alatt `traces` találja. 

    > [!NOTE]
    > Ebben a környezetben `traces` a nem ugyanaz, mint `tracing` a . Itt a az által használt telemetriatípusra utal, amely a Azure Monitor `traces` fog `AzureLogHandler` látni. Az OpenCensus egyik fogalmára hivatkozik, és az elosztott `tracing` [nyomkövetésre vonatkozik.](./distributed-tracing.md)

    > [!NOTE]
    > A gyökérszintű naplózó a FIGYELMEZTETÉS szinttel van konfigurálva. Ez azt jelenti, hogy a rendszer figyelmen kívül hagyja a kisebb súlyosságú elküldött naplókat, és nem küldi el azokat a Azure Monitor. További információt a dokumentációban [talál.](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel)

1. A további kulcsszó argumentumban egyéni tulajdonságokat is hozzáadhat a naplóüzenethez a custom_dimensions használatával.  Ezek a tulajdonságok kulcs-érték párokként jelennek meg a `customDimensions` Azure Monitor.
    > [!NOTE]
    > Ahhoz, hogy ez a funkció működjön, át kell adni egy szótárt a custom_dimensions mezőnek. Ha bármilyen más típusú argumentumot ad át, a naplózó figyelmen kívül hagyja azokat.

    ```python
    import logging

    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="configure-logging-for-django-applications"></a>Naplózás konfigurálása Django-alkalmazásokhoz

Explicit módon konfigurálhatja a naplózást az alkalmazáskódban a fentihez hasonló módon a Django-alkalmazásokhoz, vagy megadhatja a Django naplózási konfigurációjában. Ez a kód a Django-beállítások konfigurációjában használt bármelyik fájlba bemehet. A Django-beállítások konfigurálásért lásd: [Django-beállítások.](https://docs.djangoproject.com/en/3.0/topics/settings/) A naplózás konfigurálásával kapcsolatos további információkért lásd: [Django-naplózás.](https://docs.djangoproject.com/en/3.0/topics/logging/)

```json
LOGGING = {
    "handlers": {
        "azure": {
            "level": "DEBUG",
        "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
            "instrumentation_key": "<your-ikey-here>",
         },
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "stream": sys.stdout,
         },
      },
    "loggers": {
        "logger_name": {"handlers": ["azure", "console"]},
    },
}
```

Győződjön meg arról, hogy a naplózó neve megegyezik a konfigurációban megadott névvel.

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>Kivételek küldése

Az OpenCensus Python nem követi és továbbítja automatikusan a `exception` telemetriát. A rendszer kivételekkel küldi el őket a `AzureLogHandler` Python naplózási kódtárán keresztül. A normál naplózáshoz hasonló módon adhat hozzá egyéni tulajdonságokat.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
Mivel explicit módon kell naplóznunk a kivételeket, a felhasználónak kell naplóznunk a nem kezelt kivételeket. Az OpenCensus nem korlátozza, hogy a felhasználó hogyan szeretné ezt megtenni, ha explicit módon kivételtelemetelemetiát naplóz.

#### <a name="send-events"></a>Események küldése

A telemetriát ugyanúgy küldheti el, mint a telemetriát, kivéve `customEvent` `trace` a `AzureEventHandler` használatával.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>Mintavételezés

Az OpenCensus mintavételezésére vonatkozó információkért nézze meg az [OpenCensus mintavételezését.](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)

#### <a name="log-correlation"></a>Napló-összefüggések

A naplók nyomkövetési környezeti adatokkal való gazdagításával kapcsolatos részletekért lásd: OpenCensus Python-naplók [integrációja.](./correlation.md#log-correlation)

#### <a name="modify-telemetry"></a>Telemetria módosítása

A nyomonott telemetria módosításával kapcsolatos részletekért lásd: OpenCensus Python [telemetriai](./api-filtering-sampling.md#opencensus-python-telemetry-processors)processzorok Azure Monitor.


### <a name="metrics"></a>Mérőszámok

Az OpenCensus.stats 4 összesítési módszert támogat, de részleges támogatást nyújt a Azure Monitor:

- **Darabszám:** A mérési pontok számának száma. Az érték kumulatív, csak újraindításkor lehet növelni, és 0-ra áll vissza. 
- **Összeg:** A mérési pontok összege. Az érték kumulatív, csak újraindításkor lehet növelni, és 0-ra áll vissza. 
- **LastValue (Utolsó érték):** Megtartja az utolsó rögzített értéket, minden mást eldob.
- **Terjesztés:** A mérési pontok hisztogram-eloszlása. Ezt a módszert **az Azure-beli aknát nem támogatja.**

### <a name="count-aggregation-example"></a>Példa Count Aggregation (Számösszesítő)

1. Először is hozzunk létre néhány helyi metrikaadatot. Létrehozunk egy egyszerű metrikát, amely nyomon követi, hogy a felhasználó hányszor választja ki az **Enter** billentyűt.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
1. A kód ismételt futtatása arra kéri, hogy válassza az **Enter billentyűt.** Létrejön egy metrika, amely nyomon követi, hogy hányszor volt **kiválasztva az Enter.** Minden bejegyzésnél az érték növekszik, és a metrikák adatai megjelennek a konzolon. Az információk között szerepel az aktuális érték és a metrika frissítésének aktuális időbélyege.

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. Bár az értékek megadása bemutató célokra hasznos, végső soron a metrikaadatokat az Azure Monitor. Adja át a kapcsolati sztringet közvetlenül a kávénak. Azt is megadhatja egy környezeti változóban( `APPLICATIONINSIGHTS_CONNECTION_STRING` ). Módosítsa az előző lépésben lekért kódot az alábbi mintakód alapján:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

1. A gyártó a metrikaadatokat Azure Monitor küldi a rendszernek. Az alapértelmezett érték 15 másodpercenként. Egyetlen metrikát követünk nyomon, ezért a rendszer minden intervallumban elküldi ezt a metrikaadatokat a benne található értékkel és időbélyeggel együtt. Az érték kumulatív, csak újraindításkor lehet növelni, és 0-ra áll vissza. Az adatokat a alatt találja, de a `customMetrics` `customMetrics` valueCount, valueSum, valueMin, valueMax és valueStdDev tulajdonságok nincsenek hatékonyan használva.

### <a name="setting-custom-dimensions-in-metrics"></a>Egyéni dimenziók beállítása metrikákban

Az Opencensus Python SDK lehetővé teszi egyéni dimenziók hozzáadását a metrika-telemetriához a használatával, amelyek lényegében kulcs/érték párok `tags` szótárai. 

1. Szúrja be a használni kívánt címkéket a címketérképbe. A címketérkép az összes használható címke "készleteként" működik.

```python
...
tmap = tag_map_module.TagMap()
tmap.insert("url", "http://example.com")
...
```

1. Egy adott nézethez adja meg azokat a címkéket, amelyek a nézetben a címkekulccsal rögzíthető `View` metrikákhoz használhatók.

```python
...
prompt_view = view_module.View("prompt view",
                               "number of prompts",
                               ["url"], # <-- A sequence of tag keys used to specify which tag key/value to use from the tag map
                               prompt_measure,
                               aggregation_module.CountAggregation())
...
```

1. A mérési térképen rögzített címketérképet mindenképpen használja. A által megadott címkekulcsokat a rekordhoz használt `View` címketérképen kell megadni.

```python
...
mmap = stats_recorder.new_measurement_map()
mmap.measure_int_put(prompt_measure, 1)
mmap.record(tmap) # <-- pass the tag map in here
...
```

1. A tábla alatt a használatával kibocsátott összes metrikarekord `customMetrics` `prompt_view` egyéni dimenziókkal `{"url":"http://example.com"}` rendelkezik.

1. Ha különböző értékeket használó címkéket hoz létre ugyanazokkal a kulcsokkal, hozzon létre hozzájuk új címketérképeket.

```python
...
tmap = tag_map_module.TagMap()
tmap2 = tag_map_module.TagMap()
tmap.insert("url", "http://example.com")
tmap2.insert("url", "https://www.wikipedia.org/wiki/")
...
```

#### <a name="performance-counters"></a>Teljesítményszámlálók

Alapértelmezés szerint a metrikák továbbítják a teljesítményszámlálók készletét a Azure Monitor. Ezt úgy tilthatja le, hogy a jelzőt a következőre kapcsolja `enable_standard_metrics` `False` a metrika-gyártó konstruktorában: .

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

A rendszer jelenleg a következő teljesítményszámlálókat küldi el:

- Rendelkezésre álló memória (bájt)
- Processzor processzoridő (százalék)
- Bejövő kérelmek sebessége (másodpercenként)
- Bejövő kérés átlagos végrehajtási ideje (ezredmásodperc)
- Processzorhasználat feldolgozása (százalék)
- Folyamat saját bájtja (bájt)

Ezeket a metrikákat a következőben kell `performanceCounters` látnia: . További információ: [Teljesítményszámlálók.](./performance-counters.md)

#### <a name="modify-telemetry"></a>Telemetria módosítása

További információ a nyomonott telemetria módosításáról a telemetria Azure Monitor való elküldését megelőzően: OpenCensus Python [telemetriafeldolgozók.](./api-filtering-sampling.md#opencensus-python-telemetry-processors)

### <a name="tracing"></a>Nyomkövetés

> [!NOTE]
> Az OpenCensus kifejezés `tracing` az elosztott [nyomkövetést jelenti.](./distributed-tracing.md) A `AzureExporter` és `requests` a `dependency` telemetria a Azure Monitor.

1. Először is hozzunk létre helyileg néhány nyomkövetési adatot. A Python IDLE vagy a választott szerkesztőben adja meg a következő kódot:

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. A kód ismételt futtatása arra kéri, hogy adjon meg egy értéket. Minden bejegyzésnél a rendszerhéjba nyomtatja az értéket. Az OpenCensus Python-modul a megfelelő elemét hozza `SpanData` létre. Az OpenCensus projekt egy [nyomkövetési struktúrát definiál, amely egy többtartományos faként van definiálva.](https://opencensus.io/core-concepts/tracing/)
    
    ```output
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

1. Bár az értékek megadása bemutató célokra hasznos, végső soron a `SpanData` Azure Monitor. Adja át a kapcsolati sztringet közvetlenül a kávénak. Azt is megadhatja egy környezeti változóban( `APPLICATIONINSIGHTS_CONNECTION_STRING` ). Módosítsa az előző lépésben lekért kódot az alábbi mintakód alapján:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. A Python-szkript futtatásakor továbbra is meg kell adnia az értékeket, de csak az érték lesz kiírva a rendszerhéjban. A létrehozott `SpanData` okat a Azure Monitor. A kibocsátott span-adatokat a következő alatt találja: `dependencies` . További információ a kimenő kérelmekről: OpenCensus [Python-függőségek.](./opencensus-python-dependency.md)
A bejövő kérésekkel kapcsolatos további információkért lásd: OpenCensus [Python-kérések.](./opencensus-python-request.md)

#### <a name="sampling"></a>Mintavételezés

Az OpenCensus mintavételezésére vonatkozó információkért nézze meg az [OpenCensus mintavételezését.](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)

#### <a name="trace-correlation"></a>Nyomkövetési korreláció

A nyomkövetési adatok telemetriai korrelációiról az OpenCensus Python [telemetriai korrelációját tartalmazó oldalon található további információ.](./correlation.md#telemetry-correlation-in-opencensus-python)

#### <a name="modify-telemetry"></a>Telemetria módosítása

A nyomonott telemetria módosításával kapcsolatos további információkért lásd: OpenCensus Python [telemetriai](./api-filtering-sampling.md#opencensus-python-telemetry-processors)processzorok Azure Monitor.

## <a name="configure-azure-monitor-exporters"></a>A Azure Monitor konfigurálása

Ahogy látható, három különböző Azure Monitor az OpenCensus-t. Mindegyik különböző típusú telemetriát küld a Azure Monitor. Az egyes gyártók által küldött telemetriai adatok típusait az alábbi listában láthatja.

Minden egyes elfogadás elfogadja a konfiguráció ugyanazon argumentumát, és a konstruktorok között is át van va. Az egyes részleteket itt láthatja:

- `connection_string`: Az erőforráshoz való csatlakozáshoz használt Azure Monitor sztring. Elsőbbséget élvez a `instrumentation_key` felette.
- `enable_standard_metrics`: a következő hez `AzureMetricsExporter` használatos: . Jelzi a szállítót, hogy automatikusan küldjön teljesítményszámláló-metrikákat a Azure Monitor. [](../essentials/app-insights-metrics.md#performance-counters) Az alapértelmezett érték `True` a .
- `export_interval`: Az exportálás gyakoriságának másodpercekben történő megadására használatos.
- `instrumentation_key`: Az erőforráshoz való csatlakozáshoz használt Azure Monitor kulcs.
- `logging_sampling_rate`: a következő hez `AzureLogHandler` használatos: . Mintavételi sebességet [0,1,0] biztosít a naplók exportálására. Az alapértelmezett érték 1.0.
- `max_batch_size`: Az egyszerre exportált telemetria maximális méretét adja meg.
- `proxies`: Egy proxysorozatot ad meg, amely adatokat küld a Azure Monitor. További információ: [proxyk.](https://requests.readthedocs.io/en/master/user/advanced/#proxies)
- `storage_path`: A helyi tármappa helyének elérési útja (nincs telemetria). Az `opencensus-ext-azure` 1.0.3-as verziótól az alapértelmezett elérési út az operációs rendszer ideiglenes könyvtára + `opencensus-python`  +  `your-ikey` . Az 1.0.3-as előtti alapértelmezett elérési út a $USER + `.opencensus`  +  `.azure`  +  `python-file-name` .

## <a name="view-your-data-with-queries"></a>Adatok megtekintése lekérdezésekkel

Az alkalmazásból küldött telemetriai adatokat a Naplók **(Elemzés)** lapon tudja megtekinteni.

![Képernyőkép az áttekintő panelről, pirossal kijelölve a "Naplók (Analytics)" listában](./media/opencensus-python/0010-logs-query.png)

Az Active (Aktív) alatti **listában:**

- A nyomkövetési nyomkövetési Azure Monitor küldött telemetria esetében a bejövő kérések a alatt jelennek `requests` meg. A kimenő vagy a feldolgozás alatt jelennek meg `dependencies` a kérelmek.
- A metrika-Azure Monitor küldött telemetria esetében az elküldött metrikák a alatt jelennek `customMetrics` meg.
- A naplónaplók naplói által küldött Azure Monitor naplók a alatt jelennek `traces` meg. A kivételek a alatt jelennek `exceptions` meg.

A lekérdezések és naplók használatával kapcsolatos további információkért lásd: [Naplók a Azure Monitor.](../logs/data-platform-logs.md)

## <a name="learn-more-about-opencensus-for-python"></a>További információ a Pythonhoz készült OpenCensusról

* [OpenCensus Python a GitHubon](https://github.com/census-instrumentation/opencensus-python)
* [Testreszabás](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Azure Monitor a GitHubon](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus-integrációk](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor mintaalkalmazások](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Következő lépések

* [Bejövő kérések nyomon követése](./opencensus-python-dependency.md)
* [A kérések nyomon követése](./opencensus-python-request.md)
* [Alkalmazástérkép](./app-map.md)
* [Végpontok között monitorozási teljesítmény](../app/tutorial-performance.md)

### <a name="alerts"></a>Riasztások

* [Rendelkezésre állási tesztek](./monitor-web-app-availability.md): Hozzon létre teszteket, hogy megbizonyosodjon róla, oldala látható a weben.
* [Intelligens diagnosztika](./proactive-diagnostics.md): Ezek a tesztek automatikusan futnak, a beállításukhoz semmit sem kell tennie. Értesítést kap, ha az alkalmazásában szokatlanul magas a meghiúsult kérelmek száma.
* [Metrikák riasztásai:](../alerts/alerts-log.md)Riasztások beállítása, amelyek figyelmeztetik, ha egy metrika átlép egy küszöbértéket. Az alkalmazás kódjába beépített egyedi metrikákhoz is állíthat be riasztásokat.

