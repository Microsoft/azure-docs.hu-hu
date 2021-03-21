---
title: Hibák és kivételek naplózása a MSAL-ben a Pythonhoz
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan naplózhatja a hibákat és a kivételeket a MSAL for Pythonban
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1d52b017f94785f5fb25a25f127ae52d96e97d8b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578753"
---
# <a name="logging-in-msal-for-python"></a>Naplózás a Pythonhoz készült MSAL-ben

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL Python-naplózáshoz

A Pythonhoz készült MSAL-naplózás a [Python standard Library naplózási modulját](https://docs.python.org/3/library/logging.html)használja. A MSAL naplózását a következőképpen konfigurálhatja (és megtekintheti működés közben a [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Hibakeresési naplózás engedélyezése az összes modulhoz

Alapértelmezés szerint a Python-parancsfájlok naplózása ki van kapcsolva. Ha engedélyezni szeretné a részletes naplózást a parancsfájlban található **összes** Python-modulhoz, használja a következőt `logging.basicConfig` `logging.DEBUG` :

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

Ezzel a beállítással a naplózási modulnak megadott összes naplófájlt kinyomtatja a standard kimenetre.

### <a name="configure-msal-logging-level"></a>MSAL naplózási szintjének konfigurálása

A MSAL naplózási szintjét a következő módon állíthatja be a Python-napló szolgáltatójának használatával `logging.getLogger()` `"msal"` :

```python
import logging

logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="configure-msal-logging-with-azure-app-insights"></a>MSAL-naplózás konfigurálása az Azure app bepillantást

A rendszer a Python-naplókat egy olyan naplózási kezelőhöz adja, amely alapértelmezés szerint a `StreamHandler` . Ha MSAL-naplókat szeretne küldeni egy rendszerállapot-kulccsal ellátott Application Insightsre, használja a `AzureLogHandler` függvénytár által biztosítottat `opencensus-ext-azure` .

A telepítéshez `opencensus-ext-azure` adja hozzá a csomagot a PyPI-ből `opencensus-ext-azure` a függőségekhez vagy a pip telepítéséhez:

```console
pip install opencensus-ext-azure
```

Ezután módosítsa a `"msal"` naplózási szolgáltató alapértelmezett kezelőjét egy olyan példányra, amely `AzureLogHandler` a környezeti változóban a rendszerállapot-kulccsal van beállítva `APP_INSIGHTS_KEY` :

```python
import logging
import os

from opencensus.ext.azure.log_exporter import AzureLogHandler

APP_INSIGHTS_KEY = os.getenv('APP_INSIGHTS_KEY')

logging.getLogger("msal").addHandler(AzureLogHandler(connection_string='InstrumentationKey={0}'.format(APP_INSIGHTS_KEY))
```

### <a name="personal-and-organizational-data-in-python"></a>Személyes és szervezeti adatgyűjtés a Pythonban

A MSAL for Python nem naplózza a személyes és a szervezeti adatszolgáltatásokat. Nincs olyan tulajdonság, amely be-vagy kikapcsolja a személyes vagy szervezeti adatnaplózást.

A standard Python-naplózással bármit naplózhat, amit csak szeretne, de Ön felelős a bizalmas adatok biztonságos kezeléséhez és a szabályozási követelmények követéséhez.

A Pythonban történő naplózással kapcsolatos további információkért tekintse meg a Python  [naplózása: útmutató](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)című témakört.

## <a name="next-steps"></a>Következő lépések

További példákért tekintse meg a [Microsoft Identity platform Code Samples](sample-v2-code.md)című témakört.
