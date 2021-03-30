---
title: 'Rövid útmutató: bejelentkezés beállítása ASP.NET-webalkalmazáshoz'
titleSuffix: Azure AD B2C
description: Ebben a rövid útmutatóban egy olyan minta ASP.NET-webalkalmazást futtat, amely Azure Active Directory B2Ct használ a fiókok bejelentkezésének biztosításához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: devx-track-csharp, mvc
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 379ea9a0c2a49e92fa1c4882ea6e783b3b91e32f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88163917"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>Gyors útmutató: bejelentkezés beállítása ASP.NET-alkalmazáshoz Azure Active Directory B2C használatával

A Azure Active Directory B2C (Azure AD B2C) biztosítja az alkalmazások, a vállalkozások és az ügyfelek védelmét a Felhőbeli identitások kezelésével. Az Azure AD B2C nyílt szabványú protokollokkal teszi lehetővé az alkalmazások hitelesítését közösségi hálózati és vállalati fiókokon. Ebben a rövid útmutatóban egy ASP.NET-alkalmazással és egy közösségi identitásszolgáltatóval fog bejelentkezni, és meghív egy, az Azure AD B2C által védett webes API-t.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2019](https://www.visualstudio.com/downloads/) a **ASP.net és a webes fejlesztési** munkaterheléssel.
- Facebook-, Google-vagy Microsoft-beli közösségi fiók.
- [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip), vagy klónozza a mintául szolgáló webalkalmazást a GitHubról.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    A minta megoldásban két projekt található:

    - **TaskWebApp** – Ez a webalkalmazás egy feladatlistát hoz létre és szerkeszt. A webalkalmazás a **regisztrálási vagy bejelentkezési** felhasználói folyamat használatával regisztrálja vagy bejelentkezik a felhasználók számára.
    - **TaskService** – Ez a webes API a feladatlista-funkciók létrehozását, olvasását, frissítését és törlését támogatja. A webes API-nak az Azure AD B2C biztosít védelmet, és a webalkalmazással hívható meg.

## <a name="run-the-application-in-visual-studio"></a>Az alkalmazás futtatása a Visual Studióban

1. A mintaalkalmazás projektmappájában nyissa meg a **B2C-WebAPI-DotNet.sln** megoldást a Visual Studióban.
2. Ebben a rövid útmutatóban egy időben futtatja a **TaskWebApp** és a **TaskService** projektet. Kattintson a jobb gombbal a **B2C-WebAPI-DotNet** megoldásra a Megoldáskezelőben, majd válassza az **Indítási projektek beállítása** lehetőséget.
3. Válassza a **Több kezdőprojekt** elemet, és mindkét projektnél módosítsa a **Művelet** értékét **Indításra**.
4. Kattintson az **OK** gombra.
5. Nyomja le az **F5** billentyűt mindkét alkalmazás hibakereséséhez. Mindegyik alkalmazás saját böngészőlapon nyílik meg:

    - `https://localhost:44316/` – Az ASP.NET-es webalkalmazás. A gyors útmutatóban közvetlenül kommunikál az alkalmazással.
    - `https://localhost:44332/` – Az ASP.NET-es webalkalmazás által meghívott webes API.

## <a name="sign-in-using-your-account"></a>Bejelentkezés saját fiókkal

1. Kattintson a **Sign up / Sign in** (Regisztráció/Bejelentkezés) hivatkozásra az ASP.NET-es webalkalmazásban a munkafolyamat elindításához.

    ![Minta ASP.NET webalkalmazás a böngészőben a regisztráció/aláírás hivatkozás kiemelésével](./media/quickstart-web-app-dotnet/web-app-sign-in.png)

    A minta több regisztrációs beállítást is támogat, beleértve a közösségi identitásszolgáltató használatát vagy helyi fiók e-mail-címmel való létrehozását. Ebben a rövid útmutatóban a Facebook, a Google vagy a Microsoft által használt közösségi identitás-szolgáltatói fiókot használhatja.

2. A Azure AD B2C egy Fabrikam nevű fiktív cég bejelentkezési oldalát jeleníti meg a minta webalkalmazáshoz. Ha közösségi identitásszolgáltatóval szeretne regisztrálni, kattintson a használni kívánt identitásszolgáltató gombjára.

    ![Jelentkezzen be vagy regisztráljon az Identity Provider gombjait megjelenítő oldalra](./media/quickstart-web-app-dotnet/sign-in-or-sign-up-web.png)

    A hitelesítéshez (bejelentkezéshez) használja a közösségi fiók hitelesítő adatait, és engedélyezze az alkalmazásnak, hogy beolvassa az adatokat a közösségi fiókból. A hozzáférés biztosításával az alkalmazás profiladatokat kérhet le a közösségi fiókból, például a nevét és a települését.

3. Fejezze be az identitásszolgáltató bejelentkezési folyamatát.

## <a name="edit-your-profile"></a>Saját profil szerkesztése

Az Azure Active Directory B2C-funkcióival a felhasználók frissíthetik a profiljukat. A minta webalkalmazás a munkafolyamathoz tartozó Azure AD B2C szerkesztési profil felhasználói folyamatát használja.

1. Az alkalmazás menüsávján kattintson a profil nevére, és válassza az **Edit profile** (Profil szerkesztése) parancsot a létrehozott profil szerkesztéséhez.

    ![Minta webalkalmazás a böngészőben a profil szerkesztése hivatkozás kiemelésével](./media/quickstart-web-app-dotnet/edit-profile-web.png)

2. Módosítsa a **Display name** (Megjelenített név) és a **City** (Város) mezőket, majd kattintson a **Continue** (Folytatás) gombra a profil frissítéséhez.

    A módosított profil a webalkalmazás kezdőlapjának jobb felső részén jelenik meg.

## <a name="access-a-protected-api-resource"></a>Védett API-erőforrás elérése

1. Kattintson a **To-Do List** (Feladatlista) elemre a feladatlista elemeinek megadásához és módosításához.

2. Adjon meg szöveget a **New Item** (Új elem) szövegmezőben. Kattintson az **Add** (Hozzáadás) gombra az Azure AD B2C által védett, feladatlista-elemet hozzáadó webes API meghívásához.

    ![Minta webalkalmazás a böngészőben és a tennivalók listaelemének hozzáadása](./media/quickstart-web-app-dotnet/add-todo-item-web.png)

    Az ASP.NET-es webalkalmazás a védett webes API-erőforrás felé küldött, a felhasználó feladatlista-elemeire vonatkozó műveletek végrehajtására irányuló kérésbe belefoglalja az Azure AD hozzáférési jogkivonatot.

Az Azure AD B2C felhasználói fiókkal sikeresen intézett hitelesített hívást egy Azure AD B2C által védett webes API-hoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure AD B2C-bérlőt ahhoz is használhatja, ha más Azure AD B2C gyors útmutatókat vagy oktatóanyagokat is ki szeretne próbálni. Ha már nincs szüksége rá, akkor [törölheti az Azure AD B2C-bérlőt](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy minta ASP.NET-alkalmazást használt a következőhöz:

* Bejelentkezés egyéni bejelentkezési oldallal
* Jelentkezzen be egy közösségi identitás-szolgáltatóval
* Azure AD B2C fiók létrehozása
* Azure AD B2C által védett webes API meghívása

Most már hozzákezdhet saját Azure AD B2C-bérlőjének létrehozásához.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C-bérlő létrehozása az Azure Portalon](tutorial-create-tenant.md)
