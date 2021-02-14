---
title: Felhasználók számára bejelentkező webalkalmazás regisztrálása | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan regisztrálhat egy olyan webalkalmazást, amely aláírja a felhasználókat
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 96fbe676d4b929d2c1ee2efce0d10f3b5dc7c6ab
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103192"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Felhasználók számára bejelentkező webalkalmazás: alkalmazás regisztrálása

Ez a cikk ismerteti a felhasználók által bejelentkező webalkalmazások alkalmazás-regisztrációs lépéseit.

Az alkalmazás regisztrálásához a következőket használhatja:

- A [webalkalmazás](#register-an-app-by-using-the-quickstarts)rövid útmutatói. Amellett, hogy kiválóan használja az alkalmazások létrehozását, a Azure Portalban található rövid útmutatók tartalmaznak egy **, a módosítást** elnevező gombot. Ezt a gombot használhatja a szükséges tulajdonságok beállításához még egy meglévő alkalmazáshoz is. Módosítsa a tulajdonságok értékeit a saját esetére. Különösen az alkalmazás webes API URL-címe valószínűleg eltér a javasolt alapértelmezetttől, ami a kijelentkezési URI-t is érinti.
- Az Azure Portal az [alkalmazás manuális regisztrálásához](#register-an-app-by-using-the-azure-portal).
- PowerShell és parancssori eszközök.

## <a name="register-an-app-by-using-the-quickstarts"></a>Alkalmazás regisztrálása a rövid útmutatók használatával

Ezeket a hivatkozásokat használhatja a webalkalmazás létrehozásához:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Alkalmazás regisztrálása a Azure Portal használatával

> [!NOTE]
> A használni kívánt portál eltérő lehet attól függően, hogy az alkalmazás a Microsoft Azure nyilvános felhőben, vagy egy nemzeti vagy szuverén felhőben fut-e. További információ: [National felhők](./authentication-national-cloud.md#app-registration-endpoints).


1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>. 
1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
   1. Adja meg az alkalmazás **nevét** , például: `AspNetCore-WebApp` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
   1. Válassza ki az alkalmazás támogatott fiók-típusait. (Lásd: [támogatott fióktípus](./v2-supported-account-types.md).)
   1. Az **átirányítási URI** esetében adja hozzá az alkalmazás típusát és az URI célhelyet, amely a sikeres hitelesítés után a visszaadott jogkivonat-válaszokat fogja fogadni. Adja meg például a következőt: `https://localhost:44321`.
   1. Válassza a **Regisztráció** lehetőséget.
1. A **kezelés** területen válassza a **hitelesítés** lehetőséget, majd adja hozzá a következő adatokat:
   1. A **webes** szakaszban adjon hozzá `https://localhost:44321/signin-oidc` egy **átirányítási URI**-t.
   1. Az **előtérben kijelentkezési URL-cím** mezőbe írja be a értéket `https://localhost:44321/signout-oidc` .
   1. Az **implicit engedélyezés és a hibrid folyamatok** területen válassza az **azonosító tokenek** lehetőséget.
   1. Kattintson a **Mentés** gombra.
   
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Amikor megjelenik az **alkalmazás regisztrálása lap** , adja meg az alkalmazás regisztrációs adatait:
   1. Adja meg az alkalmazás **nevét** , például: `MailApp-openidconnect-v2` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
   1. Válassza ki az alkalmazás támogatott fiók-típusait. (Lásd: [támogatott fióktípus](./v2-supported-account-types.md).)
   1. Az **átirányítási URI (nem kötelező)** szakaszban a kombinált listában válassza a **web** lehetőséget, és adjon meg egy **átirányítási URI** -t `https://localhost:44326/` .
   1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
1. Az **implicit engedélyezés és a hibrid folyamatok** szakaszban válassza az **azonosító tokenek** elemet. Ehhez a mintához engedélyezni kell, hogy az [implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md) engedélyezze a bejelentkezést a felhasználó számára.
1. Kattintson a **Mentés** gombra.

# <a name="java"></a>[Java](#tab/java)

1. Amikor megjelenik az **alkalmazás regisztrálása lap** , adja meg az alkalmazás regisztrációs adatait: 
    1. Adja meg az alkalmazás **nevét** , például: `java-webapp` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók. 
    1. Válassza **a fiókok lehetőséget a szervezeti címtárban és a személyes Microsoft-fiókokban (például Skype, Xbox, Outlook.com)**.
    1. Az alkalmazás regisztrálásához válassza a **regisztráció** lehetőséget.
1. A **kezelés** területen válassza   >  **a hitelesítés platform hozzáadása** elemet.
1. Válassza a **web** lehetőséget.
1. Az **átirányítási URI** esetében adja meg ugyanazt a gazdagépet és portszámot, majd a `/msal4jsample/secure/aad` bejelentkezési oldalát. 
1. Válassza a **Konfigurálás** lehetőséget.
1. A **webes** szakaszban használja a gazdagép és a portszám értéket, majd a `/msal4jsample/graph/me` felhasználói adatok lap **átirányítási URI-ja** .
Alapértelmezés szerint a minta a következőket használja:
   - `http://localhost:8080/msal4jsample/secure/aad`
   - `http://localhost:8080/msal4jsample/graph/me`

1. Kattintson a **Mentés** gombra.
1. A **Kezelés** területen válassza a **Tanúsítványok és titkos kódok** lehetőséget.
1. Az **ügyfél titkai** szakaszban válassza az **új ügyfél titka** elemet, majd a következőket:

   1. Adja meg a kulcs leírását.
   1. Válassza ki a kulcs időtartamát **1 évben**.
   1. Válassza a **Hozzáadás** lehetőséget.
   1. Ha a kulcs értéke megjelenik, másolja később. Ez az érték nem jelenik meg újra, vagy bármilyen más módon lekérdezhető.

# <a name="python"></a>[Python](#tab/python)

1. Amikor megjelenik az **alkalmazás regisztrálása lap** , adja meg az alkalmazás regisztrációs adatait:
   1. Adja meg az alkalmazás **nevét** , például: `python-webapp` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
   1. A **támogatott fióktípus** módosítása **bármely szervezeti címtárban és személyes Microsoft-fiókban (például Skype, Xbox, Outlook.com)**.
   1. Az **átirányítási URI (nem kötelező)** szakaszban a kombinált listában válassza a **web** lehetőséget, és adja meg a következő átirányítási URI-t: `http://localhost:5000/getAToken` .
   1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Az alkalmazás **Áttekintés** lapján keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és jegyezze fel később. Ehhez a projekthez a Visual Studio konfigurációs fájlját kell konfigurálnia.
1. A **Kezelés** területen válassza a **Tanúsítványok és titkos kódok** lehetőséget.
1. Az **ügyfél titkai** szakaszban válassza az **új ügyfél titka** elemet, majd a következőket:
   1. Adja meg a kulcs leírását.
   1. Adja **meg az 1 év** kulcsának időtartamát.
   1. Válassza a **Hozzáadás** lehetőséget.
   1. Ha a kulcs értéke megjelenik, másolja. Erre később még szüksége lesz.
---

## <a name="register-an-app-by-using-powershell"></a>Alkalmazás regisztrálása a PowerShell használatával

> [!NOTE]
> Az Azure AD PowerShell jelenleg csak a következő támogatott fióktípus-típusokat hozza létre:
>
> - MyOrg (csak ebben a szervezeti könyvtárban lévő fiókok)
> - AnyOrg (fiókok bármely szervezeti címtárban)
>
> Létrehozhat egy olyan alkalmazást, amely a felhasználók személyes Microsoft-fiókjait (például Skype, Xbox vagy Outlook.com) is aláírja. Először hozzon létre egy több-bérlős alkalmazást. A támogatott fióktípus bármely szervezeti címtárban található fiókok. Ezután módosítsa a [`accessTokenAcceptedVersion`](./reference-app-manifest.md#accesstokenacceptedversion-attribute) tulajdonságot **2** értékre, a [`signInAudience`](./reference-app-manifest.md#signinaudience-attribute) tulajdonságot pedig az `AzureADandPersonalMicrosoftAccount` [alkalmazás jegyzékfájljában](./reference-app-manifest.md) a Azure Portal. További információ: 1,3. [lépés](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) a ASP.net Core oktatóanyagban. Ezt a lépést bármilyen nyelven általánosíthatja a Web Apps szolgáltatásban.

## <a name="next-steps"></a>Következő lépések

Az [alkalmazás kódjának konfigurálása](scenario-web-app-sign-user-app-configuration.md)ebben a forgatókönyvben a következő cikkre lép.
