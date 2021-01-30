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
ms.openlocfilehash: 8488325613b05d54b352a19a06860e08f1779877
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063114"
---
# <a name="logging-in-msal-for-python"></a>Naplózás a Pythonhoz készült MSAL-ben

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL Python-naplózáshoz

A MSAL Pythonban történő naplózás a szabványos Python-naplózási mechanizmust használja, például az `logging.info("msg")` alábbiak szerint konfigurálhatja a MSAL-naplózást (és megtekintheti működés közben a [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Hibakeresési naplózás engedélyezése az összes modulhoz

Alapértelmezés szerint a Python-parancsfájlok naplózása ki van kapcsolva. Ha engedélyezni szeretné a hibakeresési naplózást a teljes Python-parancsfájl összes moduljához, használja a következőt:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Csak csendben lévő MSAL-naplózás

Ha csak a MSAL szeretné meghallgatni, miközben a Python-szkript összes többi moduljában engedélyezi a hibakeresési naplózást, kapcsolja ki a MSAL Python által használt naplózó:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Személyes és szervezeti adatgyűjtés a Pythonban

A MSAL for Python nem naplózza a személyes és a szervezeti adatszolgáltatásokat. Nincs olyan tulajdonság, amely be-vagy kikapcsolja a személyes vagy szervezeti adatnaplózást.

A standard Python-naplózással bármit naplózhat, amit csak szeretne, de Ön felelős a bizalmas adatok biztonságos kezeléséhez és a szabályozási követelmények követéséhez.

A Pythonban történő naplózással kapcsolatos további információkért tekintse meg a Python  [naplózása: útmutató](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)című témakört.

## <a name="next-steps"></a>Következő lépések

További példákért tekintse meg a [Microsoft Identity platform Code Samples](sample-v2-code.md)című témakört.
