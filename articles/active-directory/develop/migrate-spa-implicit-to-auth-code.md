---
title: A JavaScript-alapú egyoldalas alkalmazások áttelepíthetők az implicit támogatásból az engedélyezési kód folyamatábrába | Azure
titleSuffix: Microsoft identity platform
description: JavaScript-SPA frissítése MSAL.js 1. x használatával és az implicit engedélyezési folyamat MSAL.js 2. x és a PKCE-és CORS-támogatással rendelkező hitelesítési kód esetében.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: fc5d9cbd02490748c17a9f40a1440c5ad0c46278
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104110"
---
# <a name="migrate-a-javascript-single-page-app-from-implicit-grant-to-auth-code-flow"></a>JavaScript-alapú egyoldalas alkalmazás migrálása implicit engedélyről az Auth Code flow-ba

A JavaScripthez készült Microsoft Authentication Library (MSAL.js) v 2.0 a Microsoft Identity platform egyoldalas alkalmazásaihoz nyújt támogatást az PKCE-és CORS-alapú engedélyezési kódokhoz. Az alábbi szakaszokban ismertetett lépéseket követve áttelepítheti a MSAL.js 1. x alkalmazást a MSAL.js 2.0 + (a továbbiakban *2. x*) és az Auth kód folyamatával.

MSAL.js 2. x a MSAL.js 1. x verzióban javítja az engedélyezési kód folyamatát a böngészőben az implicit engedélyezési folyamat helyett. MSAL.js 2. x nem **támogatja az** implicit folyamatot.

## <a name="migration-steps"></a>A migrálás lépései

Ha szeretné frissíteni az alkalmazást MSAL.js 2. x-re és az Auth kód folyamatára, három fő lépésből áll:

1. Átválthatja az [alkalmazás regisztrációs](#switch-redirect-uris-to-spa-platform) ÁTirányítási URI-ját a **webes** platformról **egyoldalas alkalmazás** platformra.
1. Frissítse a [kódját](#switch-redirect-uris-to-spa-platform) MSAL.js 1. x és **2. x** között.
1. Ha a regisztrációt megosztó összes alkalmazás frissítve lett MSAL.js 2. x és az Auth kód folyamatára, tiltsa le az [implicit hozzáférést](#disable-implicit-grant-settings) az alkalmazás regisztrálásakor.

A következő szakaszok további részletekben ismertetik az egyes lépéseket.

## <a name="switch-redirect-uris-to-spa-platform"></a>Átirányítási URI-k váltása a SPA platformra

Ha továbbra is szeretné használni a meglévő alkalmazás-regisztrációt az alkalmazásaihoz, használja a Azure Portal a regisztráció átirányítási URI-k a SPA platformra történő frissítéséhez. Ez lehetővé teszi, hogy az engedélyezési kód a PKCE és a CORS támogatással támogassa a regisztrációt használó alkalmazásokat (továbbra is frissítenie kell az alkalmazás kódját MSAL.js v2. x-re).

Kövesse az alábbi **lépéseket a webplatform-** átirányítási URI-k által jelenleg konfigurált alkalmazások regisztrálásához:

1. Jelentkezzen be a <a href="https://portal.azure.com/" target="_blank">Azure Portalba</a> , és válassza ki a **Azure Active Directory** bérlőt.
1. A **Alkalmazásregisztrációk** területen válassza ki az alkalmazást, majd a **hitelesítést**.
1. A **webplatform csempén az** URI-k **átirányítása** területen válassza a figyelmeztetési szalagcímet, amely jelzi, hogy át kell telepítenie az URI-ket.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-01-implicit-warning-banner.png" alt-text="Implicit flow figyelmeztetési szalagcím a webalkalmazás csempén Azure Portal":::
1. *Csak* azokat az átirányítási URI-ket válassza, amelyek alkalmazásai MSAL.js 2. x-et használják, majd válassza a **Konfigurálás** lehetőséget.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-02-select-redirect-uri.png" alt-text="Az átirányítási URI ablaktábla kiválasztása a SPA panelen Azure Portal":::

Ezek az átirányítási URI-k mostantól megjelennek az **egyoldalas alkalmazás** -platform csempén, amely azt mutatja, hogy az CORS-támogatás az engedélyezési kód folyamatával és a PKCE engedélyezve van ezen URI-k esetében.

:::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-03-spa-redirect-uri-tile.png" alt-text="Egyoldalas alkalmazás csempéje az alkalmazás regisztrációjában Azure Portal":::

[Új alkalmazás-regisztrációt is létrehozhat](scenario-spa-app-registration.md) , ha nem frissíti az átirányítási URI-ket a meglévő regisztráció során.

## <a name="update-your-code-to-msaljs-2x"></a>A kód frissítése MSAL.js 2. x-re

Az 1. x MSAL a [UserAgentApplication][msal-js-useragentapplication] inicializálásával létrehozott egy alkalmazás-példányt a következőképpen:

```javascript
// MSAL 1.x
import * as msal from "msal";

const msalInstance = new msal.UserAgentApplication(config);
```

A MSAL 2. x-ben inicializálja inkább a [PublicClientApplication][msal-js-publicclientapplication]:

```javascript
// MSAL 2.x
import * as msal from "@azure/msal-browser";

const msalInstance = new msal.PublicClientApplication(config);
```

A 2. x MSAL alkalmazáshoz való hozzáadásának teljes körű bemutatása: [oktatóanyag: bejelentkezés a felhasználókba és a Microsoft Graph API meghívása egy JavaScript-alapú egyoldalas alkalmazásból (Spa) az Auth Code flow használatával](tutorial-v2-javascript-auth-code.md).

Ha további módosításokat szeretne végezni a kódban, tekintse meg az [áttelepítési útmutatót](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/v1-migration.md) a githubon.

## <a name="disable-implicit-grant-settings"></a>Implicit engedélyezési beállítások letiltása

Miután frissítette az összes olyan üzemi alkalmazást, amely ezt az alkalmazást használja, és annak ügyfél-AZONOSÍTÓját MSAL 2. x-re és az engedélyezési kód folyamatára, törölje az implicit engedélyezési beállításokat az alkalmazás regisztrálásához.

Ha törli az implicit engedélyezési beállításokat az alkalmazás regisztrációjában, az implicit folyamat le lesz tiltva minden alkalmazásnál, amely a regisztrációt és az ügyfél-azonosítót használja.

Az összes alkalmazás MSAL.js 2. x és a [PublicClientApplication][msal-js-publicclientapplication]való frissítése előtt **ne tiltsa le** az implicit engedélyezési folyamatot.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az engedélyezési kód folyamatáról, beleértve az implicit és az Auth kód közötti különbségeket, tekintse meg a [Microsoft Identity platform és a OAuth 2,0 engedélyezési kód folyamatát](v2-oauth2-auth-code-flow.md).

Ha szeretné mélyebben bemutatni a JavaScript egyoldalas alkalmazás-fejlesztést a Microsoft Identity platformon, a többrészes [forgatókönyv: a cikkek egyoldalas alkalmazási](scenario-spa-overview.md) sorozata segítséget nyújt az első lépésekhez.

<!-- LINKS - external -->
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_useragentapplication_.html
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html
