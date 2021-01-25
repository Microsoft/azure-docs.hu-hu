---
title: Egyoldalas alkalmazás konfigurálása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazást (az alkalmazás kódjának konfigurációja)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 5cbb576a7fcfb2daf492a149130aa7c99fe10ac5
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753605"
---
# <a name="single-page-application-code-configuration"></a>Egyoldalas alkalmazás: kód konfigurálása

Megtudhatja, hogyan konfigurálhatja az egyoldalas alkalmazás (SPA) kódját.

## <a name="msal-libraries-for-spas-and-supported-authentication-flows"></a>MSAL-kódtárak a gyógyfürdők és a támogatott hitelesítési folyamatok számára

A Microsoft Identity platform a következő Microsoft-hitelesítési függvénytárat biztosítja a JavaScripthez (MSAL.js), hogy támogassa az implicit flow és az engedélyezési kód áramlását a PKCE az iparág által ajánlott biztonsági eljárások használatával:

| MSAL-könyvtár | Folyamat | Leírás |
|--------------|------|-------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (2. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) | Engedélyezési kód folyamatábrája (PKCE) | Egyszerű JavaScript-függvénytár bármely olyan ügyféloldali webalkalmazásban, amelyet JavaScript vagy SPA keretrendszerek, például szögletes, Vue.js és React.js használatával építettek. |
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (1. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) | Implicit folyamat | Egyszerű JavaScript-függvénytár bármely olyan ügyféloldali webalkalmazásban, amelyet JavaScript vagy SPA keretrendszerek, például szögletes, Vue.js és React.js használatával építettek. |
| ![MSAL szögletes](media/sample-v2-code/logo_angular.png) <br/> [MSAL szögletes](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Implicit folyamat | A Core MSAL.js könyvtár burkolója, amely egyszerűbbé teszi a szögletes keretrendszeren alapuló egyoldalas alkalmazásokban való használatát. |

## <a name="application-code-configuration"></a>Alkalmazás kódjának konfigurálása

Egy MSAL-könyvtárban a könyvtár inicializálásakor az alkalmazás regisztrációs adatai a konfigurációnak megfelelően továbbítódnak.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

A konfigurálható beállításokkal kapcsolatos további információkért lásd: [alkalmazás inicializálása MSAL.jssal ](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>További lépések

Lépjen be a következő cikkbe ebben a forgatókönyvben: [Bejelentkezés és kijelentkezés](scenario-spa-sign-in.md).
