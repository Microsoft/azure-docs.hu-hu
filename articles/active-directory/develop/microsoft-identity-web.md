---
title: Microsoft Identity web Authentication Library – áttekintés
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft Identity web, a hitelesítési és engedélyezési függvénytárat ASP.NET Core olyan alkalmazások számára, amelyek integrálva vannak a Azure Active Directory, a Azure AD B2C és a Microsoft Graph és más webes API-kkal.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/09/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8bc9773fa7e1b3db2e249e803f4d2a3cf39edb48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102546"
---
# <a name="microsoft-identity-web-authentication-library"></a>Microsoft Identity web Authentication Library

A Microsoft Identity web olyan ASP.NET Core kódtárak összessége, amelyek leegyszerűsítik a Microsoft Identity platformmal integrált webalkalmazásokhoz és webes API-khoz való hitelesítési és engedélyezési támogatás hozzáadását. Egy egyfelületes API kényelmi réteget biztosít, amely összekapcsolja a ASP.NET Core, a hitelesítési middleware-t és a [.net-hez készült Microsoft Authentication Library-t (MSAL)](https://github.com/azuread/microsoft-authentication-library-for-dotnet).

## <a name="supported-application-scenarios"></a>Támogatott alkalmazási helyzetek

Ha ASP.NET Core webalkalmazásokat vagy webes API-kat fejleszt, és az Azure Active Directory (Azure AD) Azure AD B2C vagy az identitás-és hozzáférés-kezelés (IAM) használatát szeretné használni, javasoljuk, hogy a Microsoft Identity web szolgáltatást a következő esetekben használja:

- [Felhasználót bejelentkeztető webalkalmazás](scenario-web-app-sign-user-overview.md)
- [Webalkalmazás, amely aláírja a felhasználókat, és egy webes API-t hív meg a nevükön](scenario-web-app-call-api-overview.md)
- [Védett webes API, amely csak a hitelesített felhasználók számára érhető el](scenario-protected-web-api-overview.md)
- [Védett webes API, amely egy másik (alsóbb szintű) webes API meghívása a bejelentkezett felhasználó nevében](scenario-web-api-call-api-overview.md)

## <a name="get-the-library"></a>A könyvtár beszerzése

A Microsoft Identity web a [NuGet](#nuget), a [.net Core projekt sablonjaiból](#project-templates)és a [githubból](#github)szerezhető be.

#### <a name="nuget"></a>NuGet

A Microsoft Identity web a NuGet-ben érhető el olyan csomagok készlete, amelyek az alkalmazás igényeinek megfelelően moduláris funkciókat biztosítanak. A .NET CLI `dotnet add` parancs vagy a Visual Studio **NuGet csomagkezelő** segítségével telepítse a projektnek megfelelő csomagokat:

- [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) – a fő csomag. A Microsoft Identity web szolgáltatást használó összes alkalmazás számára szükséges.
- [Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) – nem kötelező. Felhasználói bejelentkezéshez és kijelentkezéshez, valamint a webes alkalmazások társított vezérlőjéhez tartozó felhasználói felület hozzáadását teszi elérhetővé.
- [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) – nem kötelező. Egyszerűsített interakciót biztosít a Microsoft Graph API-val.
- [Microsoft. Identity. Web. MicrosoftGraphBeta](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraphBeta) – nem kötelező. Egyszerűsített interakciót biztosít a Microsoft Graph API [Beta-végponttal](/graph/api/overview?view=graph-rest-beta&preserve-view=true).

#### <a name="project-templates"></a>Project-sablonok

A Microsoft Identity web Project sablonjai a .NET 5,0-es verzióban érhetők el, és ASP.NET Core 3,1-projektekhez tölthetők le.

Ha a ASP.NET Core 3,1-et használja, telepítse a sablonokat a .NET parancssori felülettel:

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::1.0.0
```

Az alábbi ábrán a támogatott alkalmazások típusai és a kapcsolódó argumentumok magas szintű nézete látható:

:::image type="content" source="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" lightbox="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" alt-text="A Microsoft Identity web elérhető dot Net CLI-projektjeinek ábrája":::
<br /><sup><b>*</b></sup>nem támogatott a alkalmazásban, de aappsettings.jsbekapcsolható `MultiOrg` `webapi2` *a* bérlő beállítása `common` vagy `organizations`
<br /><sup><b>**</b></sup>`--calls-graph`Azure ad B2C esetén nem támogatott

Ez a példában szereplő .NET CLI-parancs a [Blazer Server oktatóanyagból](tutorial-blazor-server.md)származik, és létrehoz egy új, a megfelelő csomagokat és alapszintű kódot (látható helyőrző értékeket) tartalmazó új Blazer-kiszolgálói projektet:

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph --client-id "00000000-0000-0000-0000-000000000000" --tenant-id "11111111-1111-1111-1111-111111111111" --output my-blazor-app
```

#### <a name="github"></a>GitHub

A Microsoft Identity web egy, a GitHubon üzemeltetett nyílt forráskódú projekt: <a href="https://github.com/AzureAD/microsoft-identity-web" target="_blank">AzureAD/Microsoft-Identity-web</a>

A [tárház wiki](https://github.com/AzureAD/microsoft-identity-web/wiki) további dokumentációt tartalmaz, és ha segítségre van szüksége vagy hibát szeretne felderíteni, a [probléma](https://github.com/AzureAD/microsoft-identity-web/issues)beolvasható.

## <a name="features"></a>Funkciók

A Microsoft Identity web számos olyan szolgáltatást tartalmaz, amelyeket nem biztosítunk, ha az alapértelmezett ASP.NET 3,1 Project-sablonokat használja.

| Szolgáltatás                                                                                  | ASP.NET Core 3,1                                                     | Microsoft Identity web                                                                                  |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| [Felhasználói bejelentkezés](scenario-web-app-sign-user-app-configuration.md) a Web Apps szolgáltatásban             | <li>Munkahelyi vagy iskolai fiókok<li>Közösségi identitások (Azure AD B2C) | <li>Munkahelyi vagy iskolai fiókok<li>Személyes Microsoft-fiókok<li>Közösségi identitások (Azure AD B2C)     |
| [Webes API-k elleni védelem](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) | <li>Munkahelyi vagy iskolai fiókok<li>Közösségi identitások (Azure AD B2C) | <li>Munkahelyi vagy iskolai fiókok<li>Személyes Microsoft-fiókok<li>Közösségi identitások (Azure AD B2C)     |
| Kiállítói ellenőrzés a több-bérlős alkalmazásokban                                                   | No                                                                   | Igen, [minden felhők](authentication-national-cloud.md) és [Azure ad B2C](../../active-directory-b2c/index.yml) esetén |
| Webalkalmazás/API [a Microsoft Graph meghívása] [forgatókönyv-API-Call-Graph]                             | Nem                                                                   | Igen                                                                                                     |
| Webalkalmazás/API [meghívja a webes API-t] [forgatókönyv-API-Call-API]                                       | Nem                                                                   | Igen                                                                                                     |
| A tanúsítvány hitelesítő adatainak támogatása                                                         | No                                                                   | Igen, beleértve a Azure Key Vault                                                                          |
| Növekményes beleegyezett és feltételes hozzáférés támogatása a web Appsben                           | No                                                                   | Igen, MVC, Razor Pages és Blazer                                                                    |
| Jogkivonat-titkosítási tanúsítványok a webes API-kon                                                | Nem                                                                   | Igen                                                                                                     |
| [Hatókörök/alkalmazás-szerepkör ellenőrzése] [forgatókönyv-API-Validation] a webes API-kon                        | Nem                                                                   | Igen                                                                                                     |
| `WWW-Authenticate` fejléc létrehozása webes API-khoz                                         | Nem                                                                   | Igen                                                                                                     |

## <a name="next-steps"></a>Következő lépések

A Microsoft Identity web működés közbeni megtekintéséhez próbálja ki a következő útmutatót:

[Oktatóanyag: a Microsoft Identity platformot használó blézer-kiszolgálói alkalmazás létrehozása a hitelesítéshez](tutorial-blazor-server.md)

A GitHubon található Microsoft Identity web wiki részletes dokumentációt tartalmaz a könyvtár különböző szempontjaihoz. Például a tanúsítvány használata, a növekményes beleegyezés és a feltételes hozzáférés referenciája itt található:

- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates" target="_blank">Tanúsítványok használata a Microsoft. Identity. Web</a> (GitHub) használatával
- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Managing-incremental-consent-and-conditional-access" target="_blank">Növekményes engedély és feltételes hozzáférés</a> (GitHub)

<!-- LINKS -->
<!--  [miw-certs]: microsoft-identity-web-certificates.md  -->
<!--  [miw-certs-decrypt]: microsoft-identity-web-certificates.md#decryption-certificates  -->
<!--  [miw-inc-consent-ca-header]: microsoft-identity-web-consent-conditional-access.md#handling-incremental-consent-or-conditional-access-in-web-apis  -->
<!--  [miw-inc-consent-ca]: microsoft-identity-web-consent-conditional-access.md  -->
[forgatókönyv-API-Call-API]: forgatókönyv-web-API-Call-API-Call-API. MD # Option-1-Call-Microsoft-Graph-with-the-SDK  
[forgatókönyv-API-Call-Graph]: forgatókönyv-web-API-Call-API-Call-API. MD # Option-1-Call-Microsoft-Graph-with-the-SDK  
[forgatókönyv-API-érvényesítés]: scenario-protected-web-api-verification-scope-app-roles.md  
