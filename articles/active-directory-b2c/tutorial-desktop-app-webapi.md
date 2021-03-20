---
title: 'Oktatóanyag: hozzáférés biztosítása Node.js webes API-hoz egy asztali alkalmazásból'
description: Útmutató a Node.js webes API-k Active Directory B2C való használatához és a .NET Desktop-alkalmazásokból való meghívásához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 12951f25feb6f3710b8d35fbc682caeeb480e788
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555606"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Oktatóanyag: Node.js webes API-khoz való hozzáférés engedélyezése egy asztali alkalmazásból az Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan hívhat meg egy Azure Active Directory B2C (Azure AD B2C) által védett Node.js webes API-t egy Windows megjelenítési alaprendszer (WPF) asztali alkalmazásból, amelyet Azure AD B2C is véd.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API számára
> * A minta frissítése az alkalmazás használatára

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő lépéseket és előfeltételeket az [oktatóanyagban: felhasználók hitelesítése natív asztali ügyfélen](tutorial-desktop-app.md).

## <a name="add-a-web-api-application"></a>Webes API-alkalmazás hozzáadása

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetőséget nyújtanak a védett erőforrásokhoz való hozzáférés szabályozására. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. Egyes felhasználók például rendelkezhetnek olvasási és írási hozzáféréssel is, míg más felhasználóknak csak olvasási engedélye lehet. Ebben az oktatóanyagban meghatározzuk az olvasási és írási engedélyeket a webes API számára.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Az asztali alkalmazás konfigurálásakor a hatókör alatt lévő értéket jegyezze fel **, amelyet** `demo.read` egy későbbi lépésben szeretne használni. A hatókör teljes értéke hasonló a következőhöz: `https://contosob2c.onmicrosoft.com/api/demo.read` .

## <a name="grant-permissions"></a>Engedélyek megadása

A védett webes API-k natív ügyfélalkalmazások általi meghívásához meg kell adnia a regisztrált natív ügyfélalkalmazás-engedélyeket a Azure AD B2C regisztrált webes API-hoz.

Az előfeltételként szolgáló oktatóanyagban regisztrált egy *nativeapp1* nevű natív ügyfélalkalmazás. A következő lépésekkel konfigurálhatja, hogy a natív alkalmazás regisztrálása az előző szakaszban *webapi1* elérhető API-hatókörökkel történjen. Ez lehetővé teszi, hogy az asztali alkalmazás olyan hozzáférési jogkivonatot szerezzen be Azure AD B2Ctól, amelyet a webes API használhat az erőforrásokhoz való hatókörön belüli hozzáférés ellenőrzéséhez és biztosításához. Az oktatóanyag későbbi részében az asztali alkalmazás és a webes API-kód is konfigurálható és futtatható.

Az alkalmazások Azure AD B2C-bérlőben való regisztrálásához használhatja az új, egységes **Alkalmazásregisztrációk** -élményt vagy az örökölt  **alkalmazások (örökölt)** felületét. [További információ az új felületről](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Alkalmazásregisztrációk](#tab/app-reg-ga/)

1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza ki azt a natív ügyfélalkalmazás-alkalmazást, amelynek hozzáféréssel kell RENDELKEZNIE az API-hoz. Például: *nativeapp1*.
1. A **kezelés** területen válassza az **API-engedélyek** lehetőséget.
1. A **konfigurált engedélyek** területen válassza **az engedély hozzáadása** elemet.
1. Válassza a **saját API** -k fület.
1. Válassza ki azt az API-t, amelyhez hozzáférést szeretne adni a natív ügyfélalkalmazás számára. Például: *webapi1*.
1. Az **engedély** területen bontsa ki a **bemutató** elemet, majd válassza ki a korábban definiált hatóköröket. Például: *bemutató. Read* és *demo. Write*.
1. Válassza az **engedélyek hozzáadása** lehetőséget. Az utasítás szerint várjon néhány percet, mielőtt továbblép a következő lépésre.
1. Válassza a **rendszergazdai jóváhagyás megadása (a bérlő neve)** lehetőséget.
1. Válassza ki a jelenleg bejelentkezett rendszergazdai fiókot, vagy jelentkezzen be egy olyan fiókkal a Azure AD B2C-bérlőben, amely legalább a *Cloud Application Administrator* szerepkörhöz van rendelve.
1. Válassza ki az **Elfogadás** lehetőséget.
1. Válassza a **frissítés** lehetőséget, majd ellenőrizze, hogy a "engedélyezve..." mindkét hatókör **állapota** alatt jelenik meg. Eltarthat néhány percig, amíg az engedélyek propagálása megtörténik.

#### <a name="applications-legacy"></a>[Alkalmazások (örökölt)](#tab/applications-legacy/)

1. Válassza az **alkalmazások (örökölt)** lehetőséget, majd válassza a *nativeapp1* lehetőséget.
1. Válassza az **API-hozzáférés** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. Az **API kiválasztása** legördülő menüben válassza a *webapi1* lehetőséget.
1. A **hatókörök kiválasztása** legördülő menüben válassza ki a korábban definiált hatóköröket. Például: *bemutató. Read* és *demo. Write*.
1. Válassza az **OK** lehetőséget.

* * *

A WPF asztali alkalmazás használatához a felhasználó az Azure AD B2C-vel hitelesíti magát. Az asztali alkalmazás lekéri az engedélyezést az Azure AD B2C-ből a védett webes API-hoz való hozzáféréshez.

## <a name="configure-the-samples"></a>A minták konfigurálása

Most, hogy regisztrálta a webes API-t, és hatóköröket és engedélyeket konfigurált, konfigurálja az asztali alkalmazást és a webes API-mintákat a Azure AD B2C bérlő használatára.

### <a name="update-the-desktop-application"></a>Asztali alkalmazás frissítése

A cikk előfeltétele, hogy egy [WPF asztali alkalmazást](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) módosítson, hogy engedélyezze a bejelentkezést egy felhasználói folyamattal a Azure ad B2C-bérlőben. Ebben a szakaszban ugyanazt az alkalmazást frissíti, hogy a korábban regisztrált webes API-ra, a *webapi1*-ra hivatkozzon.

1. Nyissa meg az **Active-Directory-B2C-WPF** megoldást ( `active-directory-b2c-wpf.sln` ) a Visual Studióban.
1. Az **Active Directory-B2C-WPF** projektben nyissa meg az *app. XAML. cs* fájlt, és keresse meg az alábbi változók definícióit.
    1. Cserélje le a változó értékét `ApiScopes` a **demo. Read** hatókör meghatározásakor korábban rögzített értékre.
    1. Cserélje le a változó értékét `ApiEndpoint` a korábban feljegyzett **átirányítási URI-ra** , amikor a bérlőben regisztrálta a webes API-t (például *webapi1*).

    Bemutatunk egy példát:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>A Node.js API-minta beolvasása és frissítése

Ezután szerezze be a Node.js webes API-kód mintát a GitHubról, és konfigurálja úgy, hogy az Azure AD B2C-bérlőben regisztrált webes API-t használja.

[Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip), vagy a klónozza a mintául szolgáló webalkalmazást a GitHubról.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

A mintául szolgáló Node.js webes API a Passport.js kódtárat használja arra, hogy engedélyezze az Azure AD B2C-nek az API felé irányuló hívások védelmét.

1. Nyissa meg az `index.js` fájlt.
1. Frissítse ezeket a változók definícióit a következő értékekkel. Váltson a `<web-API-application-ID>` korábban regisztrált webes API **alkalmazás-(ügyfél-) azonosítójára** (*webapi1*). Váltson `<your-b2c-tenant>` a Azure ad B2C bérlő nevére.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Mivel helyileg futtatja az API-t, frissítse a GET metódus elérési útját a `/` bemutató alkalmazás helye helyett `/hello` :

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>A minták futtatása

### <a name="run-the-nodejs-web-api"></a>A Node.js webes API futtatása

1. Indítsa el a Node.js parancssort.
2. Váltson arra a könyvtárra, amelyben a Node.js-minta található. Például: `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Futtassa az alábbi parancsot:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Asztali alkalmazás futtatása

1. Nyissa meg az **Active-Directory-B2C-WPF** megoldást a Visual Studióban.
2. Az asztali alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. Jelentkezzen be a [felhasználók asztali alkalmazáson belüli, az Azure Active Directory B2C-vel történő hitelesítésével](tutorial-desktop-app.md) foglalkozó oktatóanyagban használt e-mail-címmel és jelszóval.
4. Válassza az **API hívása** gombot.

Az asztali alkalmazás egy kérést küld a helyileg futó webes API-nak, és egy érvényes hozzáférési jogkivonat ellenőrzése után megjeleníti a bejelentkezett felhasználó megjelenítendő nevét.

![A WPF asztali alkalmazás felső ablaktábláján megjelenő megjelenítendő név](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

A Azure AD B2C által védett asztali alkalmazás a helyileg futó webes API-t hívja meg, amelyet Azure AD B2C is véd.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API számára
> * A minta frissítése az alkalmazás használatára

> [!div class="nextstepaction"]
> [Identitás-szolgáltatók hozzáadása az alkalmazásokhoz Azure Active Directory B2C](add-identity-provider.md)