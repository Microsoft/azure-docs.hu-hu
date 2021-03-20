---
title: 'Oktatóanyag: hozzáférés biztosítása egy ASP.NET webes API-hoz'
titleSuffix: Azure AD B2C
description: Útmutató a ASP.NET webes API-k és a ASP.NET-webalkalmazások általi meghívásához a Active Directory B2C használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 84bf0ddd194cd59a7e728c0c50dbe3cbad2afa4a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555642"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Oktatóanyag: hozzáférés biztosítása egy ASP.NET webes API-hoz a Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan hívhat meg egy védett webes API-erőforrást Azure Active Directory B2C (Azure AD B2C) egy ASP.NET-webalkalmazásból.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API számára
> * A minta beállítása az alkalmazás használatára

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő lépéseket és előfeltételeket az [oktatóanyagban: a hitelesítés engedélyezése webalkalmazásokban Azure Active Directory B2C használatával](tutorial-web-app-dotnet.md).

## <a name="add-a-web-api-application"></a>Webes API-alkalmazás hozzáadása

A webes API-erőforrásokat regisztrálni kell a bérlőn, mielőtt azok elfogadják és reagálni tudják a védett erőforrásokra vonatkozó kéréseket olyan ügyfélalkalmazások számára, amelyek hozzáférési jogkivonatot mutatnak be.

Az alkalmazások Azure AD B2C-bérlőben való regisztrálásához használhatja az új, egységes **Alkalmazásregisztrációk** -élményt vagy az örökölt  **alkalmazások (örökölt)** felületét. [További információ az új felületről](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Alkalmazásregisztrációk](#tab/app-reg-ga/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza az **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *webapi1*.
1. Az **átirányítási URI** területen válassza a **web** lehetőséget, majd adjon meg egy végpontot, amelyben Azure ad B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta helyileg fut, és figyeli a következőt: `https://localhost:44332` .
1. Válassza a **Regisztráció** lehetőséget.
1. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.

#### <a name="applications-legacy"></a>[Alkalmazások (örökölt)](#tab/applications-legacy/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **alkalmazások (örökölt)** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
5. Adjon nevet az alkalmazásnak. Például: *webapi1*.
6. A **Web App/web API belefoglalásához** válassza az **Igen** lehetőséget.
7. A **Válasz URL-cím** mezőben adjon meg egy végpontot, amelyben Azure ad B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta helyileg fut, és figyeli a következőt: `https://localhost:44332` .
8. Az **alkalmazás-azonosító URI-ja** mezőben adja meg a webes API-hoz használt azonosítót. A teljes azonosító URI-t, a tartománnyal együtt, a rendszer hozza létre. Például: `https://contosotenant.onmicrosoft.com/api`.
9. Kattintson a **Létrehozás** lehetőségre.
10. A Tulajdonságok lapon jegyezze fel az alkalmazás AZONOSÍTÓját, amelyet a webalkalmazás konfigurálásakor használni fog.

* * *

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetőséget nyújtanak a védett erőforrásokhoz való hozzáférés szabályozására. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. A webes API-k bizonyos felhasználói például rendelkezhetnek olvasási és írási hozzáféréssel is, míg mások csak olvasási hozzáféréssel. Ebben az oktatóanyagban hatókörök segítségével határozzuk meg az olvasási és írási engedélyeket a webes API számára.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Engedélyek megadása

Ha egy védett webes API-t szeretne meghívni egy alkalmazásból, meg kell adnia az alkalmazás engedélyeit az API-nak. Az előfeltételként szolgáló oktatóanyagban létrehozott egy webalkalmazást a *webapp1* nevű Azure ad B2Cban. Ezt az alkalmazást használhatja a webes API meghívásához.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Az alkalmazás regisztrálva van a védett webes API meghívásához. A felhasználók a Azure AD B2C használatával hitelesítik az alkalmazást. Az alkalmazás a védett webes API-hoz való hozzáféréshez Azure AD B2C engedélyezési engedélyt kap.

## <a name="configure-the-sample"></a>A minta konfigurálása

Most, hogy regisztrálta a webes API-t, és hatókörök vannak definiálva, a webes API-t a Azure AD B2C bérlő használatára konfigurálja. Ebben az oktatóanyagban egy mintául szolgáló webes API-t fog konfigurálni. A minta webes API az előfeltételként megadott oktatóanyagban letöltött projekt részét képezi.

Két projekt szerepel a mintául szolgáló megoldásban:

* **TaskWebApp** – Feladatlista létrehozása és szerkesztése. A minta a **regisztrációs vagy bejelentkezési** felhasználói folyamat használatával regisztrálja vagy bejelentkezik a felhasználók számára.
* **TaskService** – támogatja a Feladatlista létrehozását, olvasását, frissítését és törlését. Az API-t a Azure AD B2C és a TaskWebApp által védettnek nevezzük.

### <a name="configure-the-web-application"></a>A webalkalmazás konfigurálása

1. Nyissa meg a **B2C-WebAPI-DotNet** megoldást a Visual Studióban.
1. A **TaskWebApp** projektben nyissa meg **Web.config**.
1. Az API helyi futtatásához használja az **api:TaskServiceUrl** localhost-beállítást. Az alábbiak szerint módosítsa a Web.config fájlt:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. Konfigurálja az API URI-ját. Ez az az URI, amelyet a webalkalmazás az API-kérelem elvégzéséhez használ. A kért engedélyeket is konfigurálja.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>A webes API konfigurálása

1. A **TaskService** projektben nyissa meg **Web.config**.
1. Konfigurálja az API-t a bérlő használatához.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Állítsa be az ügyfél-azonosítót a regisztrált webes API-alkalmazás *webapi1*.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Frissítse a felhasználói folyamat beállítását a regisztrációs és bejelentkezési felhasználói folyamat nevével, *B2C_1_signupsignin1*.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Konfigurálja a hatókörök beállítást a portálon létrehozott beállításoknak megfelelően.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>Minta futtatása

A **TaskWebApp** és a **TaskService** projektet is futtatnia kell.

1. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, és válassza az **Indítási projektek beállítása...** lehetőséget.
1. **Több indítási projektet** is kijelölhet.
1. Mindkét projektnél módosítsa a **Művelet** értékét **Indításra**.
1. A konfiguráció mentéséhez kattintson **az OK** gombra.
1. Nyomja le az **F5** gombot mindkét alkalmazás futtatásához. Minden alkalmazás saját böngészőablakban nyílik meg.
    * `https://localhost:44316/` a webalkalmazás.
    * `https://localhost:44332/` a webes API.

1. A webalkalmazásban válassza a **regisztráció/bejelentkezés** lehetőséget a webalkalmazásba való bejelentkezéshez. Használja a korábban létrehozott fiókot.
1. A bejelentkezést követően válassza a **Feladatlista** elemet, és hozzon létre feladatlistát.

Ha feladatlistát hoz létre, a webalkalmazás egy kérést küld a webes API-nak a Feladatlista elem létrehozásához. A védett webalkalmazás a Azure AD B2C által védett webes API-t hívja meg.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API számára
> * A minta beállítása az alkalmazás használatára

> [!div class="nextstepaction"]
> [Identitás-szolgáltatók hozzáadása az alkalmazásokhoz Azure Active Directory B2C](add-identity-provider.md)