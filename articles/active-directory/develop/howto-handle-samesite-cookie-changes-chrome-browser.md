---
title: A SameSite-cookie-változások kezelése a Chrome böngészőben | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan kezelheti a SameSite-cookie-módosításokat a Chrome böngészőben.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 30c4f054259aa7c3f2a9fdfaeeadd64f26dd9bea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94444911"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>SameSite-cookie-k változásainak kezelése a Chrome böngészőben

## <a name="what-is-samesite"></a>Mi az a SameSite?

`SameSite` a egy olyan tulajdonság, amely HTTP-cookie-kon állítható be, hogy megakadályozza a helyek közötti kérelmek hamisításának (CSRF) támadásait a webalkalmazásokban:

- Ha a `SameSite` értéke **LAX**, a cookie-t a rendszer ugyanazon a helyen és a más helyekről érkező kérésekre küldött kérelmekben továbbítja. A rendszer nem fogadja el a tartományok közötti GET kérésekben.
- A **szigorú** érték biztosítja, hogy a cookie-t csak ugyanazon a helyen belül küldje el a rendszer a kérelmekben.

Alapértelmezés szerint az érték nincs beállítva a böngészőkben, ezért a rendszer nem `SameSite` korlátozza a kérésekben a cookie-k küldésére vonatkozó korlátozásokat. Egy alkalmazásnak a CSRF-védelemre kell kattintania a **LAX** vagy **szigorú** beállításával.

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite változások és a hitelesítés hatása

[A SameSite vonatkozó szabványok legújabb frissítései](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) az alkalmazások védelmének biztosítását teszik elérhetővé azáltal, hogy az alapértelmezett viselkedés az, `SameSite` Ha nincs megadva érték a LAX értékre. Ez a megoldás azt jelenti, hogy a cookie-k a HTTP-kérelmekre korlátozottak lesznek, kivéve a más helyekről történő letöltést. Emellett a **none** érték nem lesz bevezetve az elküldött cookie-k korlátozásának eltávolításához. Ezek a frissítések hamarosan a Chrome böngésző egy későbbi verziójában jelennek meg.

Ha a webalkalmazások a "form_post" válasz mód használatával hitelesítik a Microsoft Identity platformot, a bejelentkezési kiszolgáló egy HTTP-POST használatával válaszol az alkalmazásra, hogy elküldje a jogkivonatokat vagy az hitelesítési kódot. Mivel ez a kérelem egy tartományok közötti kérelem (a `login.microsoftonline.com` tartományból `https://contoso.com/auth` ), az alkalmazás által beállított cookie-k már a Chrome új szabályai alá tartoznak. A helyek közötti forgatókönyvekben használandó cookie-k az *állapotot* *és az* egyszer használatos értékeket tartalmazó cookie-k, amelyeket a rendszer a bejelentkezési kérelemben is elküld. Az Azure AD más cookie-kat is eldobott a munkamenet tárolására.

Ha nem frissíti a webalkalmazásokat, ez az új viselkedés a hitelesítési hibákhoz vezet.

## <a name="mitigation-and-samples"></a>Enyhítés és minták

A hitelesítési hibák elhárítása érdekében a Microsoft Identity platformmal hitelesítő webalkalmazások megadhatják a `SameSite` tulajdonságot a tartományok `None` közötti forgatókönyvekben használt cookie-k számára a Chrome böngészőben történő futtatáskor.
Más böngészők [(lásd a](https://www.chromium.org/updates/same-site/incompatible-clients) teljes listát) kövesse a korábbi viselkedését, `SameSite` és ha `SameSite=None` be van állítva, a cookie-k nem tartalmazzák.
Ezért a több böngészőből álló webalkalmazások hitelesítésének támogatásához `SameSite` csak a Chrome-ra kell beállítani az értéket, `None` és más böngészőkben üresen kell hagyni az értéket.

Ezt a módszert az alábbi kódrészletek szemléltetik.

# <a name="net"></a>[.NET](#tab/dotnet)

Az alábbi táblázat azokat a lekéréses kérelmeket mutatja be, amelyek a SameSite megváltoztak a ASP.NET és a ASP.NET Core mintákban.

| Sample | Lekéréses kérelem |
| ------ | ------------ |
|  [ASP.NET Core Web App növekményes oktatóanyaga](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Ugyanazon a helyen lévő cookie-javítás #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET MVC webalkalmazás minta](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Ugyanazon a helyen lévő cookie-javítás #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [Active-Directory-DotNet-rendszergazda-korlátozott-hatókörök-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Ugyanazon a helyen lévő cookie-javítás #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

a SameSite-cookie-k ASP.NET és ASP.NET Core való kezelésével kapcsolatos részletekért lásd még:

- [SameSite-cookie-k használata ASP.net Coreban](/aspnet/core/security/samesite) .
- [ASP.NET blog a SameSite-probléma megoldásához](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Sample |
| ------ |
|  [MS-Identity-Python-WebApp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Sample | Lekéréses kérelem |
| ------ | ------------ |
|  [MS-Identity-Java-WebApp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Ugyanazon a helyen lévő cookie-javítás #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [MS-Identity-Java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Ugyanazon a helyen lévő cookie-javítás #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Következő lépések

További információ a SameSite és a webalkalmazási forgatókönyvről:

- [Google Chrome GYIK a SameSite-on](https://www.chromium.org/updates/same-site/faq)

- [Króm SameSite lapja](https://www.chromium.org/updates/same-site)

- [Forgatókönyv: a felhasználók által bejelentkezett webalkalmazás](scenario-web-app-sign-user-overview.md)