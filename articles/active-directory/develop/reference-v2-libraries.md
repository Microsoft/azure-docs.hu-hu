---
title: Microsoft Identity platform hitelesítési kódtárak | Azure
description: A Microsoft Identity platformmal kompatibilis ügyféloldali kódtárak és köztes szoftverek listája. Ezekkel a tárakkal a felhasználói bejelentkezés (hitelesítés) és a védett webes API-hozzáférés (Engedélyezés) támogatását veheti igénybe az alkalmazásaihoz.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 590e57d587c8e6e254811892b5c5e740b511c302
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104690653"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft Identity platform hitelesítési kódtárak

Az alábbi táblázatok a Microsoft hitelesítési függvénytárának támogatását mutatják be több alkalmazás típusához. Ide tartoznak a könyvtár forráskódra mutató hivatkozások, ahol lekérheti az alkalmazás projektjeinek csomagját, valamint azt, hogy a könyvtár támogatja-e a felhasználói bejelentkezést (hitelesítést), a védett webes API-khoz (engedélyezéshez) való hozzáférést vagy mindkettőt.

A Microsoft Identity platformot az OpenID Foundation tanúsította [hitelesített OpenID-szolgáltatóként](https://openid.net/certification/). Ha a Microsoft Authentication Library (MSAL) vagy más, a Microsoft által támogatott tár helyett inkább egy könyvtárat szeretne használni, válasszon egy [hitelesített OpenID Connect-implementációt](https://openid.net/developers/certified/).

Ha úgy dönt, hogy a [OAuth 2,0 vagy az OpenID Connect 1,0](active-directory-v2-protocols.md)saját protokoll szintű implementációját adja meg, ügyeljen arra, hogy az egyes szabványokra vonatkozó előírásokban Kiemelt figyelmet szenteljenek a biztonsági szempontoknak, és a [Microsoft SDL][Microsoft-SDL]-hez hasonló, a szoftverfejlesztői életciklussal (SDL) kapcsolatos módszertant kell követnie.

## <a name="single-page-application-spa"></a>Egyoldalas alkalmazás (SPA)

Egy egyoldalas alkalmazás teljes egészében a böngésző felületén fut, és dinamikusan vagy alkalmazás betöltési idején kéri le az oldal (HTML, CSS és JavaScript) adatmennyiségét. Meghívhatja a webes API-kat a háttérbeli adatforrásokkal való kommunikációra.

Mivel a SPA kódja teljes egészében a böngészőben fut, olyan *nyilvános ügyfélnek* minősül, amely nem tudja biztonságosan tárolni a titkokat.

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="web-application"></a>Webalkalmazás

A webalkalmazások kódot futtatnak egy olyan kiszolgálón, amely HTML-, CSS-és JavaScript-adatokat hoz létre és küld a felhasználó webböngészőjébe. A felhasználó identitását a rendszer a felhasználó böngészője (az előtér) és a webkiszolgáló (a háttér) közötti munkamenetként tartja karban.

Mivel a webalkalmazás kódja a webkiszolgálón fut, egy olyan *bizalmas ügyfélnek* tekintendő, amely biztonságosan képes tárolni a titkokat.

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

## <a name="desktop-application"></a>Asztali alkalmazás

Az asztali alkalmazások általában bináris (lefordított) kódok, amelyek felfedik a felhasználói felületet, és a felhasználó asztalán futnak.

Mivel egy asztali alkalmazás a felhasználó asztalán fut, olyan *nyilvános ügyfélnek* minősül, amely nem tudja biztonságosan tárolni a titkokat.

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="mobile-application"></a>Mobilalkalmazás

A mobil alkalmazások általában bináris (lefordított) kódok, amelyek felfedik a felhasználói felületet, és egy felhasználó mobileszközön futnak.

Mivel a mobil alkalmazások a felhasználó mobileszközön futnak, olyan *nyilvános ügyfélnek* számít, amely nem tudja biztonságosan tárolni a titkokat.

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="service--daemon"></a>Szolgáltatás/démon

A szolgáltatások és démonok általában a kiszolgálók közötti és a felügyelet nélküli (más néven *fej*-alapú) kommunikációhoz használatosak. Mivel nincs felhasználó a billentyűzeten a hitelesítő adatok megadásához vagy az erőforrás-hozzáféréshez való belefoglaláshoz, ezek az alkalmazások önmagukban hitelesítik magukat, nem pedig felhasználóként, amikor a rendszer jogosult hozzáférést kér a webes API erőforrásaihoz.

Egy kiszolgálón futó szolgáltatás vagy démon *bizalmas ügyfélnek* minősül, amely biztonságosan tárolhatja a titkos kulcsait.

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="next-steps"></a>Következő lépések

További információ a Microsoft hitelesítési könyvtáráról: a [Microsoft Authentication Library (MSAL) áttekintése](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
