---
title: Függőségi nyomon követés az Azure Application Insights a OpenCensus Pythonban | Microsoft Docs
description: A Python-alkalmazások függőségi hívásait a OpenCensus Python használatával figyelheti.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: a8f673295236d60ec6681bbfaee1201a4659674b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585712"
---
# <a name="track-dependencies-with-opencensus-python"></a>Függőségek követése a OpenCensus Pythonban

A függőségek az alkalmazás által hívott külső összetevők. A függőségi adatok gyűjtése a OpenCensus Python és annak különböző integrációi használatával történik. Ezután a rendszer elküldi az adat Application Insights Azure Monitor alatt `dependencies` telemetria.

Először is a Python-alkalmazást a legújabb [OpenCensus PYTHON SDK](./opencensus-python.md)-val alakíthatja ki.

## <a name="in-process-dependencies"></a>Folyamaton belüli függőségek

A OpenCensus Python SDK for Azure Monitor lehetővé teszi a "folyamaton belüli" függőségi telemetria küldését (az alkalmazáson belül előforduló információkat és logikát). A folyamaton belüli függőségek a `type` mezővel is rendelkeznek az `INPROC` elemzés során.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>"Kérelmek" integrációs függőségek

A kimenő kérelmek nyomon követése a OpenCensus- `requests` integrációval.

Töltse le és telepítse a `opencensus-ext-requests` [PyPI](https://pypi.org/project/opencensus-ext-requests/) -ből, és adja hozzá a nyomkövetési integrációhoz. A Python- [kérelmek](https://pypi.org/project/requests/) könyvtárának használatával küldött kérelmeket a rendszer nyomon fogja követni.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>Függőségek a "httplib" integrációval

A kimenő kérelmek nyomon követése a OpenCensus- `httplib` integrációval.

Töltse le és telepítse a `opencensus-ext-httplib` [PyPI](https://pypi.org/project/opencensus-ext-httplib/) -ből, és adja hozzá a nyomkövetési integrációhoz. A [http. Client](https://docs.python.org/3.7/library/http.client.html) Python3 vagy [httplib](https://docs.python.org/2/library/httplib.html) for Python2 szolgáltatással küldött kérelmeket a rendszer nyomon fogja követni.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>Függőségek a "Django" integrációval

A kimenő Django-kérelmek nyomon követése a OpenCensus- `django` integrációval.

> [!NOTE]
> Az egyetlen követett kimenő Django-kérelem egy adatbázisra irányuló hívás. A Django alkalmazásra irányuló kérelmekért lásd: [bejövő kérelmek](./opencensus-python-request.md#tracking-django-applications).

Töltse le és telepítse a `opencensus-ext-django` [PyPI](https://pypi.org/project/opencensus-ext-django/) -ból, és adja hozzá a következő sort a `MIDDLEWARE` Django fájl szakaszához `settings.py` .

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

További beállítások is megadhatók, a [testreszabások](https://github.com/census-instrumentation/opencensus-python#customization) a teljes referenciához olvashatók.

```python
OPENCENSUS = {
    'TRACE': {
        'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
        'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
            connection_string="InstrumentationKey=<your-ikey-here>"
        )''',
    }
}
```

## <a name="dependencies-with-mysql-integration"></a>Függőségek a "MySQL" integrációval

A MYSQL-függőségek nyomon követése a OpenCensus- `mysql` integrációval. Ez az integráció támogatja a [MySQL-Connector](https://pypi.org/project/mysql-connector-python/) függvénytárat.

Töltse le és telepítse a `opencensus-ext-mysql` [PyPI](https://pypi.org/project/opencensus-ext-mysql/) -ből, és adja hozzá a következő sorokat a kódhoz.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Függőségek a "pymysql" integrációval

Kövesse nyomon a PyMySQL függőségeit a OpenCensus- `pymysql` integrációval.

Töltse le és telepítse a `opencensus-ext-pymysql` [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) -ből, és adja hozzá a következő sorokat a kódhoz.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Függőségek a "PostgreSQL" integrációval

A PostgreSQL függőségeinek nyomon követése a OpenCensus- `postgresql` integrációval. Ez az integráció támogatja a [psycopg2](https://pypi.org/project/psycopg2/) könyvtárat.

Töltse le és telepítse a `opencensus-ext-postgresql` [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) -ből, és adja hozzá a következő sorokat a kódhoz.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Függőségek a "pymongo" integrációval

Kövesse nyomon a MongoDB függőségeit a OpenCensus- `pymongo` integrációval. Ez az integráció támogatja a [pymongo](https://pypi.org/project/pymongo/) könyvtárat.

Töltse le és telepítse a `opencensus-ext-pymongo` [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) -ből, és adja hozzá a következő sorokat a kódhoz.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Függőségek a "SQLAlchemy" integrációval

A függőségek követése a SQLAlchemy használatával a OpenCensus- `sqlalchemy` integráció használatával. Ez az integráció nyomon követi a [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) -csomag használatát, függetlenül az alapul szolgáló adatbázistól.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Következő lépések

* [Alkalmazástérkép](./app-map.md)
* [Rendelkezésre állás](./monitor-web-app-availability.md)
* [Search](./diagnostic-search.md)
* [Log (Analytics) lekérdezés](../logs/log-query-overview.md)
* [Tranzakció diagnosztikája](./transaction-diagnostics.md)

