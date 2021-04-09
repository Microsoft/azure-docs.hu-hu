---
title: Hibák és kivételek naplózása MSAL.js
titleSuffix: Microsoft identity platform
description: Útmutató a hibák és kivételek naplózásához MSAL.js
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
ms.openlocfilehash: d7463e3376847a219750254548c25ca79f4cdfb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954817"
---
# <a name="logging-in-msaljs"></a>Naplózás az MSAL.js-ben

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msaljs"></a>Naplózás konfigurálása MSAL.js

Az MSAL.js (JavaScript) naplózásának engedélyezése egy naplózó objektum átadásával a konfiguráció során egy példány létrehozásához `UserAgentApplication` . Ez a naplózó objektum a következő tulajdonságokkal rendelkezik:

- `localCallback`: egy visszahívási példány, amelyet a fejlesztő biztosíthat a naplók egyéni módon történő felhasználásához és közzétételéhez. A localCallback metódus implementálása attól függően, hogy hogyan szeretné átirányítani a naplókat.
- `level` (nem kötelező): a konfigurálható naplózási szint. A támogatott naplózási szintek a következők:,, `Error` `Warning` `Info` és `Verbose` . A mező alapértelmezett értéke: `Info`.
- `piiLoggingEnabled` (nem kötelező): ha igaz értékre van állítva, a személyes és szervezeti adatnaplókat naplózza. Alapértelmezés szerint ez hamis, így az alkalmazás nem naplózza a személyes adatait. A személyes adatnaplókat soha nem írja az alapértelmezett kimenetekre, például a konzolra, a Logcat vagy a NSLog.
- `correlationId` (nem kötelező): egyedi azonosító, amely a kérésnek a hibakeresési célú hozzárendelésére szolgál. Az alapértelmezett érték a RFC4122 4-es verziójának GUID-azonosítója (128 bit).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
    system: {
        logger: new Msal.Logger(
            loggerCallback , {
                level: Msal.LogLevel.Verbose,
                piiLoggingEnabled: false,
                correlationId: '1234'
            }
        )
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="next-steps"></a>Következő lépések

További példákért tekintse meg a [Microsoft Identity platform Code Samples](sample-v2-code.md)című témakört.