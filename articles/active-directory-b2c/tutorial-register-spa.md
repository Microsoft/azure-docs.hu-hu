---
title: 'Oktatóanyag: egyoldalas alkalmazás regisztrálása'
titleSuffix: Azure AD B2C
description: Ebből az oktatóanyagból megtudhatja, hogyan regisztrálhat egy egyoldalas alkalmazást (SPA) Azure Active Directory B2C a Azure Portal használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d2eaf1dce432821dcfc693dc69dcf975a3d8be8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92503861"
---
# <a name="tutorial-register-a-single-page-application-spa-in-azure-active-directory-b2c"></a>Oktatóanyag: egyoldalas alkalmazás (SPA) regisztrálása Azure Active Directory B2C

Az [alkalmazások](application-types.md) Azure Active Directory B2C (Azure ad B2C) való interakció előtt regisztrálni kell őket egy Ön által kezelt bérlőben. Ez az oktatóanyag bemutatja, hogyan regisztrálhat egy egyoldalas alkalmazást ("SPA") a Azure Portal használatával.

## <a name="overview-of-authentication-options"></a>A hitelesítési beállítások áttekintése

Számos modern webalkalmazás úgy van kialakítva, mint az ügyféloldali egyoldalas alkalmazások ("gyógyfürdők"). A fejlesztők a JavaScript vagy a SPA-keretrendszer, például a szögletes, a Vue és a reakciók használatával írhatják őket. Ezek az alkalmazások egy webböngészőben futnak, és különböző hitelesítési jellemzőkkel rendelkeznek, mint a hagyományos kiszolgálóoldali webes alkalmazások.

Azure AD B2C **két** lehetőséget kínál az egyoldalas alkalmazások bejelentkezésére a felhasználók számára, és jogkivonatok beszerzése a háttér-szolgáltatások vagy webes API-k eléréséhez:

### <a name="authorization-code-flow-with-pkce"></a>Engedélyezési kód folyamatábrája (PKCE)
- [OAuth 2,0 engedélyezési kód folyamata (PKCE)](./authorization-code-flow.md). Az engedélyezési kód folyamatábrája lehetővé teszi, hogy az alkalmazás hitelesítő kódot cseréljen az **azonosító** jogkivonatok számára a védett API-k meghívásához szükséges hitelesített felhasználói és **hozzáférési** jogkivonatok megjelenítéséhez. Emellett visszaadja azokat a **frissítési** jogkivonatokat, amelyek hosszú távú hozzáférést biztosítanak az erőforrásokhoz a felhasználók nevében anélkül, hogy ezekkel a felhasználókkal való interakcióra lenne szükség. 

Ez az **ajánlott** módszer. A korlátozott élettartamú frissítési tokenek segítségével az alkalmazás alkalmazkodik a [modern böngésző cookie-i adatvédelmi korlátaihoz](../active-directory/develop/reference-third-party-cookies-spas.md), például a Safari itp-hez.

A folyamat előnyeit kihasználva az alkalmazás olyan hitelesítési függvénytárat használhat, amely támogatja azt, például [MSAL.js 2. x](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa). 

![Egyoldalas alkalmazások – hitelesítés](./media/tutorial-single-page-app/spa-app-auth.svg)

### <a name="implicit-grant-flow"></a>Implicit engedélyezési folyamat
- [OAuth 2,0 implicit folyamat](implicit-flow-single-page-application.md). Egyes keretrendszerek, például [MSAL.js 1. x](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp), csak az implicit engedélyezési folyamatot támogatják. Az implicit engedélyezési folyamat lehetővé teszi, hogy az alkalmazás **azonosítót** és **hozzáférési** jogkivonatokat kapjon. Az engedélyezési kód folyamatával ellentétben az implicit engedélyezési folyamat nem ad vissza **frissítési jogkivonatot**. 

![Egyoldalas alkalmazások – implicit](./media/tutorial-single-page-app/spa-app.svg)

Ez a hitelesítési folyamat nem tartalmaz olyan alkalmazási helyzeteket, amelyek platformfüggetlen JavaScript-keretrendszereket használnak, például az Electron-et és a reakciós Natívt. Ezek a forgatókönyvek további képességeket igényelnek a natív platformokkal való interakcióhoz.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ha még nem hozott létre saját [Azure ad B2C bérlőt](tutorial-create-tenant.md), hozzon létre egyet most. Meglévő Azure AD B2C bérlőt is használhat.

## <a name="register-the-spa-application"></a>A SPA-alkalmazás regisztrálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza az **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *spaapp1*.
1. A **támogatott fióktípus** területen válassza a **fiókok lehetőséget bármely identitás-szolgáltató vagy szervezeti címtárban (a felhasználók felhasználói folyamatokkal történő hitelesítéséhez)**
1. Az **átirányítási URI** területen válassza az **egylapos alkalmazás (Spa)** lehetőséget, majd írja be `https://jwt.ms` az URL szövegmezőbe.

    Az átirányítási URI azt a végpontot, amelyhez a felhasználót az engedélyezési kiszolgáló Azure AD B2C (ebben az esetben) a felhasználóval való interakció befejezése után küldi el a rendszer, és a sikeres engedélyezéshez hozzáférési jogkivonatot vagy engedélyezési kódot küld. Éles alkalmazásokban ez általában egy nyilvánosan elérhető végpont, amelyben az alkalmazás fut, például: `https://contoso.com/auth-response` . Az oktatóanyaghoz hasonló tesztelési célokra beállíthatja, hogy a egy `https://jwt.ms` Microsoft tulajdonú webalkalmazás, amely egy jogkivonat dekódolású tartalmát jeleníti meg (a jogkivonat tartalma soha nem hagyja el a böngészőt). Az alkalmazásfejlesztés során hozzáadhatja azt a végpontot, amelyben az alkalmazás helyileg, például a következőt figyeli `http://localhost:5000` . A regisztrált alkalmazásokban bármikor hozzáadhat és módosíthat átirányítási URI-ket.

    Az átirányítási URI-k esetében a következő korlátozások érvényesek:

    * A válasz URL-címének a sémával kell kezdődnie `https` , hacsak nem használja `localhost` .
    * A válasz URL-címe megkülönbözteti a kis-és nagybetűket. Az esetnek egyeznie kell a futó alkalmazás URL-címének elérési útjával. Ha például az alkalmazás az elérési út részeként szerepel `.../abc/response-oidc` , ne adja meg `.../ABC/response-oidc` a válasz URL-címét. Mivel a webböngésző a kis-és nagybetűket megkülönböztető módon kezeli az elérési utakat, a hozzájuk társított cookie-k `.../abc/response-oidc` kizárható, ha a rendszer átirányítja a kis-és nagybetűket `.../ABC/response-oidc` .

1. Az **engedélyek** területen jelölje be a *rendszergazdai jóváhagyás megadása az OpenID-hez és a offline_access engedélyekhez* jelölőnégyzetet.
1. Válassza a **Regisztráció** lehetőséget.


## <a name="enable-the-implicit-flow"></a>Az implicit folyamat engedélyezése
Az implicit folyamat használata esetén engedélyeznie kell az implicit engedélyezési folyamatot az alkalmazás regisztrálásakor.

1. A bal oldali menüben kattintson a **kezelés** elemre, majd válassza a **hitelesítés** lehetőséget.
1. Az **implicit engedélyezés** területen jelölje be a **hozzáférési jogkivonatok** és az **azonosító tokenek** jelölőnégyzetet.
1. Kattintson a **Mentés** gombra.

## <a name="migrate-from-the-implicit-flow"></a>Migrálás az implicit folyamatból

Ha már van olyan alkalmazás, amely az implicit folyamatot használja, javasoljuk, hogy az azt támogató keretrendszer használatával áttelepíti az engedélyezési kód folyamatát, például [MSAL.js 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

Ha az alkalmazás regisztrációja által reprezentált összes éles egyoldalas alkalmazás az engedélyezési kód folyamatát használja, tiltsa le az implicit engedélyezési folyamat beállításait. 

1. A bal oldali menüben kattintson a **kezelés** elemre, majd válassza a **hitelesítés** lehetőséget.
1. Az **implicit engedélyezés** területen törölje a **hozzáférési tokenek** és az **azonosító jogkivonatok** jelölőnégyzet jelölését.
1. Kattintson a **Mentés** gombra.

Az implicit folyamatot használó alkalmazások továbbra is működőképesek maradnak, ha az implicit folyamat engedélyezve van (be van jelölve).

* * *

## <a name="next-steps"></a>Következő lépések

Következő lépésként megtudhatja, hogyan hozhat létre felhasználói folyamatokat, hogy a felhasználók regisztrálhatják, bejelentkezhetnek és kezelhetik a profiljaikat.

> [!div class="nextstepaction"]
> [Felhasználói folyamatok létrehozása Azure Active Directory B2C >](tutorial-create-user-flows.md)
