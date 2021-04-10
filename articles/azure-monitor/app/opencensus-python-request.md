---
title: Bejövő kérelmek nyomon követése az Azure Application Insights a OpenCensus Pythonban | Microsoft Docs
description: A Python-alkalmazások OpenCensus Python-n keresztüli hívásának figyelése.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: 3b029a9cb14a81c80072847dc17d6b71f480743f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585674"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>A beérkező kérelmek nyomon követése a OpenCensus Pythonban

A beérkező kérések adatai a OpenCensus Python és annak különböző integrációi használatával gyűjthetők össze. Nyomon követheti a beérkező kérések adatait, amelyeket a népszerű webes keretrendszerekre épülő webalkalmazásoknak küldenek `django` `flask` `pyramid` . Ezután a rendszer elküldi az adat Application Insights Azure Monitor alatt `requests` telemetria.

Először is a Python-alkalmazást a legújabb [OpenCensus PYTHON SDK](./opencensus-python.md)-val alakíthatja ki.

## <a name="tracking-django-applications"></a>Django-alkalmazások nyomon követése

1. Töltse le és telepítse a `opencensus-ext-django` [PyPI](https://pypi.org/project/opencensus-ext-django/) -ből, és az alkalmazást a middleware-mel `django` . Az alkalmazásnak küldött bejövő kéréseket a `django` rendszer nyomon fogja követni.

2. Adja `opencensus.ext.django.middleware.OpencensusMiddleware` meg a `settings.py` fájlt a fájlban `MIDDLEWARE` .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Győződjön meg arról, hogy a AzureExporter megfelelően van konfigurálva a `settings.py` alatt `OPENCENSUS` . A nem nyomon követni kívánt URL-címekről érkező kérések hozzáadásához vegye fel a következőt: `EXCLUDELIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>A lombik alkalmazásainak nyomon követése

1. Töltse le és telepítse a `opencensus-ext-flask` [PyPI](https://pypi.org/project/opencensus-ext-flask/) -ből, és az alkalmazást a middleware-mel `flask` . Az alkalmazásnak küldött bejövő kéréseket a `flask` rendszer nyomon fogja követni.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. Az alkalmazást a használatával is konfigurálhatja `flask` `app.config` . A nem nyomon követni kívánt URL-címekről érkező kérések hozzáadásához vegye fel a következőt: `EXCLUDELIST_PATHS` .

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Piramis-alkalmazások nyomon követése

1. Töltse le és telepítse a `opencensus-ext-django` [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) -ból, és az alkalmazását a Tween-mel `pyramid` . Az alkalmazásnak küldött bejövő kéréseket a `pyramid` rendszer nyomon fogja követni.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Az `pyramid` Tween-t közvetlenül a kódban is konfigurálhatja. A nem nyomon követni kívánt URL-címekről érkező kérések hozzáadásához vegye fel a következőt: `EXCLUDELIST_PATHS` .

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="tracking-fastapi-applications"></a>FastAPI-alkalmazások nyomon követése

A OpenCensus nem rendelkezik FastAPI-bővítménysel. A saját FastAPI-middleware megírásához hajtsa végre a következő lépéseket:

1. A következő függőségek szükségesek: 
    - [fastapi](https://pypi.org/project/fastapi/)
    - [uvicorn](https://pypi.org/project/uvicorn/)

2. [FastAPI-middleware](https://fastapi.tiangolo.com/tutorial/middleware/)hozzáadása. Győződjön meg arról, hogy a span Kind-kiszolgálót állítja be: `span.span_kind = SpanKind.SERVER` .

3. Futtassa az alkalmazást. A FastAPI alkalmazásban indított hívásokat automatikusan nyomon kell követni, és a telemetria-t közvetlenül kell naplózni Azure Monitor.

    ```python 
    # Opencensus imports
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    from opencensus.trace.span import SpanKind
    from opencensus.trace.attributes_helper import COMMON_ATTRIBUTES
    # FastAPI imports
    from fastapi import FastAPI, Request
    # uvicorn
    import uvicorn

    app = FastAPI()

    HTTP_URL = COMMON_ATTRIBUTES['HTTP_URL']
    HTTP_STATUS_CODE = COMMON_ATTRIBUTES['HTTP_STATUS_CODE']

    # fastapi middleware for opencensus
    @app.middleware("http")
    async def middlewareOpencensus(request: Request, call_next):
        tracer = Tracer(exporter=AzureExporter(connection_string=f'InstrumentationKey={APPINSIGHTS_INSTRUMENTATIONKEY}'),sampler=ProbabilitySampler(1.0))
        with tracer.span("main") as span:
            span.span_kind = SpanKind.SERVER

            response = await call_next(request)

            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_STATUS_CODE,
                attribute_value=response.status_code)
            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_URL,
                attribute_value=str(request.url))

        return response

    @app.get("/")
    async def root():
        return "Hello World!"

    if __name__ == '__main__':
        uvicorn.run("example:app", host="127.0.0.1", port=5000, log_level="info")
    ```

## <a name="next-steps"></a>Következő lépések

* [Alkalmazástérkép](./app-map.md)
* [Rendelkezésre állás](./monitor-web-app-availability.md)
* [Search](./diagnostic-search.md)
* [Log (Analytics) lekérdezés](../logs/log-query-overview.md)
* [Tranzakció diagnosztikája](./transaction-diagnostics.md)

