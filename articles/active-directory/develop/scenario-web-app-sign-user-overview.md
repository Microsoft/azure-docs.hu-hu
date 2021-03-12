---
title: Felhasználók bejelentkezni egy webalkalmazásból | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre olyan webalkalmazást, amely aláírja a felhasználókat (áttekintés)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 960c00f516aac0c804718e439ae3ed9691759329
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010677"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Forgatókönyv: a felhasználók által bejelentkezett webalkalmazás

Mindent megtudhat a Microsoft Identity platformot használó webalkalmazások létrehozásához a felhasználói bejelentkezéshez.

## <a name="getting-started"></a>Első lépések

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Ha az első olyan hordozható (ASP.NET Core) webalkalmazást szeretné létrehozni, amely a felhasználók számára jelentkezik be, kövesse az alábbi rövid útmutatót:

[Gyors útmutató: ASP.NET Core webalkalmazás, amely bejelentkezik a felhasználók számára](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Ha szeretné megismerni, hogyan adhat hozzá bejelentkezést egy meglévő ASP.NET-webalkalmazáshoz, próbálja meg a következő rövid útmutatót:

[Rövid útmutató: ASP.NET-webalkalmazás, amely bejelentkezik a felhasználók számára](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Ha Ön Java-fejlesztő, próbálja ki a következő rövid útmutatót:

[Gyors útmutató: bejelentkezés felvétele a Microsofttal egy Java-webalkalmazásba](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Ha Python-fejlesztést végez, próbálja ki a következő rövid útmutatót:

[Gyors útmutató: bejelentkezés felvétele a Microsofttal egy Python-webalkalmazásba](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Áttekintés

A webalkalmazáshoz olyan hitelesítést adhat hozzá, amellyel bejelentkezhet a felhasználókba. A hitelesítés hozzáadásával a webalkalmazás elérheti a korlátozott profilokra vonatkozó információkat, így testre szabhatja a felhasználói élményt.

A webalkalmazások a felhasználókat egy böngészőben hitelesítik. Ebben az esetben a webalkalmazás irányítja a felhasználó böngészőjében, hogy bejelentkezzen a Azure Active Directoryba (Azure AD). Az Azure AD egy bejelentkezési választ ad vissza a felhasználó böngészőjén keresztül, amely a felhasználóhoz tartozó jogcímeket tartalmaz egy biztonsági jogkivonatban. A felhasználók bejelentkezésekor a rendszer kihasználja a [nyílt ID-kapcsolat](./v2-protocols-oidc.md) szabványos protokolljának előnyeit, amelyet a middleware- [kódtárak](scenario-web-app-sign-user-app-configuration.md#microsoft libraries supporting web apps)használatával egyszerűsített.

![Webalkalmazás-jelek a felhasználókban](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Második fázisként engedélyezheti, hogy az alkalmazás a bejelentkezett felhasználó nevében hívja meg a webes API-kat. Ez a következő fázis egy másik forgatókönyv, amelyet a [webes API-kat meghívó webalkalmazásban](scenario-web-app-call-api-overview.md)talál.

> [!NOTE]
> Ha bejelentkezik a webalkalmazásba, a webalkalmazás védelme és a felhasználói jogkivonat érvényesítése is megtörténik.  .NET esetén ehhez a forgatókönyvhöz még nincs szükség a Microsoft Authentication Library (MSAL) szolgáltatásra, amely a védett API-k meghívására szolgáló jogkivonat beszerzésére vonatkozik. A hitelesítési kódtárak a követési forgatókönyvben lesznek bevezetve, amikor a webalkalmazásnak webes API-kat kell meghívnia.

## <a name="specifics"></a>Sajátosságai

- Az alkalmazás regisztrációja során meg kell adnia egy vagy több (ha több helyen helyezi üzembe az alkalmazást) válasz URI-k. Bizonyos esetekben (ASP.NET és ASP.NET Core) engedélyeznie kell az azonosító tokent. Végezetül egy kijelentkezési URI-t kell beállítania, hogy az alkalmazás feliratkozik a felhasználókra.
- Az alkalmazás kódjában meg kell adnia azt a szolgáltatót, amelyhez a webalkalmazás bejelentkezett. Előfordulhat, hogy testre szeretné szabni a jogkivonat-ellenőrzést (különösen a partneri forgatókönyvekben).
- A webalkalmazások bármilyen fióktípus használatát támogatják. További információ: [támogatott fióktípus](v2-supported-account-types.md).

## <a name="recommended-reading"></a>Ajánlott olvasás

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Következő lépések

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Lépjen tovább a forgatókönyv következő cikkére, amely az [alkalmazás regisztrálását](./scenario-web-app-sign-user-app-registration.md?tabs=aspnetcore)ismerteti.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Lépjen tovább a forgatókönyv következő cikkére, amely az [alkalmazás regisztrálását](./scenario-web-app-sign-user-app-registration.md?tabs=aspnet)ismerteti.

# <a name="java"></a>[Java](#tab/java)

Lépjen tovább a forgatókönyv következő cikkére, amely az [alkalmazás regisztrálását](./scenario-web-app-sign-user-app-registration.md?tabs=java)ismerteti.

# <a name="python"></a>[Python](#tab/python)

Lépjen tovább a forgatókönyv következő cikkére, amely az [alkalmazás regisztrálását](./scenario-web-app-sign-user-app-registration.md?tabs=python)ismerteti.

---