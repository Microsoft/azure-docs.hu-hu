---
title: 'Oktatóanyag: felhasználók hitelesítése natív ügyfélalkalmazás'
titleSuffix: Azure AD B2C
description: Útmutató a .NET desktop-alkalmazások felhasználói bejelentkezésének biztosításához a Azure Active Directory B2C használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06d27c3a3daa4702653a2063d0ac70fd094e2d74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "78186199"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Oktatóanyag: felhasználók hitelesítése natív asztali ügyfélprogramban Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan használható a Azure Active Directory B2C (Azure AD B2C) a bejelentkezéshez, és hogyan regisztrálhat felhasználókat egy Windows megjelenítési alaprendszer (WPF) asztali alkalmazásban. Azure AD B2C lehetővé teszi az alkalmazások számára, hogy a nyílt szabványos protokollok használatával hitelesítsék a közösségi fiókokat, a vállalati fiókokat és a Azure Active Directory fiókokat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A natív ügyfélalkalmazás hozzáadása
> * A minta beállítása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Felhasználói folyamatokat hozhat létre](tutorial-create-user-flows.md) , amelyekkel engedélyezheti az alkalmazás felhasználói élményét.
- Telepítse a [Visual Studio 2019](https://www.visualstudio.com/downloads/) -et a **.net desktop fejlesztői** és **ASP.net, valamint a webes fejlesztési** számítási feladatokkal.

## <a name="add-the-native-client-application"></a>A natív ügyfélalkalmazás hozzáadása

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.

## <a name="configure-the-sample"></a>A minta konfigurálása

Ebben az oktatóanyagban egy olyan mintát konfigurál, amelyet letöltheti a GitHubról. A minta WPF asztali alkalmazás a regisztrálást, a bejelentkezést és a védett webes API-t hívhat Azure AD B2Cban. [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip), [böngésszen az adattárban](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop), vagy klónozza a GitHubon található mintát.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Ha frissíteni szeretné az alkalmazást a Azure AD B2C-Bérlővel való együttműködésre, és az alapértelmezett bemutató bérlője helyett meghívja a felhasználói folyamatokat:

1. Nyissa meg az **Active-Directory-B2C-WPF** megoldást ( `active-directory-b2c-wpf.sln` ) a Visual Studióban.
2. Az **Active Directory-B2C-WPF** projektben nyissa meg az *app. XAML. cs* fájlt, és keresse meg az alábbi változók definícióit. A helyére cserélje le a `{your-tenant-name}` Azure ad B2C bérlő nevét és `{application-ID}` a korábban feljegyzett alkalmazás-azonosítót.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. Frissítse a szabályzat nevének változóit az előfeltételek részeként létrehozott felhasználói folyamatok nevével. Például:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>Minta futtatása

A minta létrehozásához és futtatásához nyomja le az **F5** billentyűt.

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

1. Válassza a **Bejelentkezés** lehetőséget a felhasználóként való regisztrációhoz. Ez a **B2C_1_signupsignin1** felhasználói folyamatot használja.
2. A Azure AD B2C egy bejelentkezési lapot jelenít meg a **regisztráció most** hivatkozásra kattintva. Mivel még nem rendelkezik fiókkal, válassza a **regisztráció most** hivatkozást.
3. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A regisztrációs munkafolyamat a felhasználó jelszavát és a felhasználói folyamatban definiált kért attribútumokat is gyűjti.

    Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket.

    ![A bejelentkezési vagy bejelentkezési munkafolyamat részeként megjelenő regisztrációs oldal](./media/tutorial-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. A **Létrehozás** gombra kattintva helyi fiókot hozhat létre a Azure ad B2C bérlőben.

A felhasználó mostantól az e-mail-címük használatával jelentkezhet be, és használhatja az asztali alkalmazást. Sikeres regisztráció vagy bejelentkezés után a rendszer megjeleníti a token részleteit a WPF alkalmazás alsó ablaktábláján.

![A WPF asztali alkalmazás alsó ablaktábláján megjelenő jogkivonat részletei](./media/tutorial-desktop-app/desktop-app-01-post-signin.png)

Ha az **API hívása** gombot választja, **hibaüzenet** jelenik meg. A hiba oka, hogy jelenlegi állapotában az alkalmazás megpróbál hozzáférni a bemutató bérlő által védett API-hoz `fabrikamb2c.onmicrosoft.com` . Mivel a hozzáférési jogkivonata csak az Ön Azure AD B2C bérlője számára érvényes, az API-hívás ezért nem engedélyezett.

Folytassa a következő oktatóanyaggal, amely egy védett webes API-t regisztrál a saját bérlőben, és lehetővé teszi a **hívási API** működőképességét.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A natív ügyfélalkalmazás hozzáadása
> * A minta beállítása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

Ezután a **Call API** gomb funkciójának engedélyezéséhez adja meg a WPF asztali alkalmazás hozzáférését a saját Azure ad B2C bérlőben regisztrált webes API-hoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: hozzáférés biztosítása Node.js webes API-hoz egy asztali alkalmazásból >](tutorial-desktop-app-webapi.md)
