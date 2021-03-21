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
ms.openlocfilehash: fe73832ec5eaee62a2dc2d397c12f82334e2efd8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010702"
---
# <a name="single-page-application-code-configuration"></a>Egyoldalas alkalmazás: kód konfigurálása

Megtudhatja, hogyan konfigurálhatja az egyoldalas alkalmazás (SPA) kódját.

## <a name="microsoft-libraries-supporting-single-page-apps"></a>Egyoldalas alkalmazásokat támogató Microsoft-kódtárak 

A következő Microsoft-kódtárak támogatják az egyoldalas alkalmazásokat:

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

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

## <a name="next-steps"></a>Következő lépések

Lépjen be a következő cikkbe ebben a forgatókönyvben: [Bejelentkezés és kijelentkezés](scenario-spa-sign-in.md).
