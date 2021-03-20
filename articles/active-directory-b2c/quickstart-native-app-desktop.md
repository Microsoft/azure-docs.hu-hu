---
title: 'Gyors útmutató: asztali alkalmazások bejelentkezésének beállítása'
titleSuffix: Azure AD B2C
description: Ebben a rövid útmutatóban egy Azure Active Directory B2C-t használó minta WPF Desktop-alkalmazást futtathat a fiók bejelentkezésének biztosításához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebed2f5e8664bd4336219f9387b8d27c8f3a1c59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "78187304"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Gyors útmutató: Asztali alkalmazásba való bejelentkezés konfigurálása Azure Active Directory B2C-vel

A Azure Active Directory B2C (Azure AD B2C) biztosítja az alkalmazások, a vállalkozások és az ügyfelek védelmét a Felhőbeli identitások kezelésével. Az Azure AD B2C nyílt szabványú protokollokkal teszi lehetővé az alkalmazások hitelesítését közösségi hálózati és vállalati fiókokon. Ebben a rövid útmutatóban egy asztali Windows megjelenítési alaprendszerbeli (WPF-) alkalmazással jelentkezik be egy közösségi identitásszolgáltatót használva, és az Azure AD B2C által védett webes API-t hív meg.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2019](https://www.visualstudio.com/downloads/) a **ASP.net és a webes fejlesztési** munkaterheléssel.
- Facebook-, Google-vagy Microsoft-beli közösségi fiók.
- [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip) , vagy klónozott [Azure-Samples/Active-Directory-B2C-DotNet-Desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) adattárát a githubról.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Az alkalmazás futtatása a Visual Studióban

1. A mintaalkalmazás projektmappájában nyissa meg az **active-directory-b2c-wpf.sln** megoldást a Visual Studióban.
2. Nyomja le az **F5** billentyűt az alkalmazás hibakereséséhez.

## <a name="sign-in-using-your-account"></a>Bejelentkezés saját fiókkal

1. Kattintson a **Sign in** (Bejelentkezés) gombra a **Sign Up or Sign In** (Regisztráció vagy bejelentkezés) munkafolyamat elindításához.

    ![Képernyőfelvétel a minta WPF-alkalmazásról](./media/quickstart-native-app-desktop/wpf-sample-application.png)

    A minta több regisztrációs lehetőséget is támogat. Ezek közé tartozik a közösségi identitás-szolgáltató használata vagy helyi fiók létrehozása e-mail-cím használatával. Ebben a rövid útmutatóban a Facebook, a Google vagy a Microsoft által használt közösségi identitás-szolgáltatói fiókot használhatja.


2. A Azure AD B2C egy Fabrikam nevű fiktív cég bejelentkezési oldalát jeleníti meg a minta webalkalmazáshoz. Ha közösségi identitásszolgáltatóval szeretne regisztrálni, kattintson a használni kívánt identitásszolgáltató gombjára.

    ![Bejelentkezési vagy regisztrációs oldal, amely az identitás-szolgáltatókat jeleníti meg](./media/quickstart-native-app-desktop/sign-in-or-sign-up-wpf.png)

    A hitelesítéshez (bejelentkezéshez) használja a közösségi fiók hitelesítő adatait, és engedélyezze az alkalmazásnak, hogy beolvassa az adatokat a közösségi fiókból. A hozzáférés biztosításával az alkalmazás profiladatokat kérhet le a közösségi fiókból, például a nevét és a települését.

2. Fejezze be az identitásszolgáltató bejelentkezési folyamatát.

    Az új fiókprofil részletei előre ki vannak töltve a közösségi fiókja adataival.

## <a name="edit-your-profile"></a>Saját profil szerkesztése

Az Azure AD B2C-funkcióival a felhasználók frissíthetik a profiljukat. A minta webalkalmazás a munkafolyamathoz tartozó Azure AD B2C szerkesztési profil felhasználói folyamatát használja.

1. Az alkalmazás menüsávján kattintson az **Edit profile** (Profil szerkesztése) gombra a létrehozott profil szerkesztéséhez.

    ![A WPF-minta alkalmazásban Kiemelt profil szerkesztése gomb](./media/quickstart-native-app-desktop/edit-profile-wpf.png)

2. Válassza ki a létrehozott fiókhoz rendelt identitásszolgáltatót. Ha például a Facebookot használta identitás-szolgáltatóként a fiók létrehozásakor, a Facebook lehetőségre kattintva módosíthatja a társított profil részleteit.

3. Módosítsa a **Display name** (Megjelenítendő név) és a **City** (Város) mezőt, majd kattintson a **Continue** (Folytatás) gombra.

    Ekkor a *Token info* (Jogkivonat adatai) szövegmezőben megjelenik egy új hozzáférési jogkivonat. Ha szeretné ellenőrizni a profilján végzett módosításokat, másolja és illessze be a hozzáférési jogkivonatot a https://jwt.ms jogkivonat-dekóderbe.

## <a name="access-a-protected-api-resource"></a>Védett API-erőforrás elérése

Kattintson a **Call API** (API meghívása) elemre, amellyel kérelmet küldhet a védett erőforrásnak.

![API meghívása](./media/quickstart-native-app-desktop/call-api-wpf.png)

Az alkalmazás a védett webes API-erőforrás felé küldött kérésbe belefoglalja az Azure AD hozzáférési jogkivonatot. A webes API a hozzáférési jogkivonatban található megjelenített nevet küldi vissza.

Az Azure AD B2C felhasználói fiókkal sikeresen intézett hitelesített hívást egy Azure AD B2C által védett webes API-hoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure AD B2C-bérlőt ahhoz is használhatja, ha más Azure AD B2C gyors útmutatókat vagy oktatóanyagokat is ki szeretne próbálni. Ha már nincs szüksége rá, akkor [törölheti az Azure AD B2C-bérlőt](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy minta asztali alkalmazást használt a következőre:

* Bejelentkezés egyéni bejelentkezési oldallal
* Jelentkezzen be egy közösségi identitás-szolgáltatóval
* Azure AD B2C fiók létrehozása
* Azure AD B2C által védett webes API meghívása

Most már hozzákezdhet saját Azure AD B2C-bérlőjének létrehozásához.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C-bérlő létrehozása az Azure Portalon](tutorial-create-tenant.md)
