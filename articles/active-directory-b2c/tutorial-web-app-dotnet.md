---
title: 'Oktatóanyag: hitelesítés engedélyezése webalkalmazásokban'
titleSuffix: Azure AD B2C
description: Arra vonatkozó útmutató, hogyan használhatja az Azure Active Directory B2C-t felhasználói bejelentkezés biztosításához egy ASP.NET-es webalkalmazás esetén.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 9c3c63b6116e02e8a742b69e90c11e182d72ab2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953030"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Oktatóanyag: hitelesítés engedélyezése webalkalmazásokban Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan használható a Azure Active Directory B2C (Azure AD B2C) a bejelentkezéshez, és hogyan regisztrálhat felhasználókat egy ASP.NET-webalkalmazásban. Azure AD B2C lehetővé teszi az alkalmazások számára, hogy a nyílt szabványos protokollok használatával hitelesítsék a közösségi fiókokat, a vállalati fiókokat és a Azure Active Directory fiókokat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás frissítése Azure AD B2C
> * A minta beállítása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Ez az oktatóanyag egy ASP.NET minta webalkalmazást használ. Más példákhoz (beleértve a ASP.NET Core, a Node.js, a Python és más alkalmazások esetében) lásd: [Azure Active Directory B2C](code-samples.md).

## <a name="prerequisites"></a>Előfeltételek

* [Felhasználói folyamatokat hozhat létre](tutorial-create-user-flows.md) , amelyekkel engedélyezheti az alkalmazás felhasználói élményét.
* Telepítse a [Visual Studio 2019](https://www.visualstudio.com/downloads/) alkalmazást a **ASP.net és a webes fejlesztési** munkaterheléssel.

## <a name="update-the-application-registration"></a>Az alkalmazás regisztrációjának frissítése

Az előfeltételek részeként elvégzett oktatóanyagban egy webalkalmazást regisztrált Azure AD B2Cban. Az oktatóanyagban szereplő példával való kommunikáció engedélyezéséhez hozzá kell adnia egy átirányítási URI-t, és létre kell hoznia egy ügyfél titkos kulcsot (kulcs) a regisztrált alkalmazáshoz.

### <a name="add-a-redirect-uri-reply-url"></a>Átirányítási URI hozzáadása (válasz URL-címe)

Ha frissíteni szeretne egy alkalmazást a Azure AD B2C-bérlőben, használhatja az új egyesített **Alkalmazásregisztrációk** -élményt vagy az örökölt  **alkalmazások (örökölt)** felületét. [További információ az új felületről](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Alkalmazásregisztrációk](#tab/app-reg-ga/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, válassza a **birtokolt alkalmazások** fület, majd válassza ki a *webapp1* alkalmazást.
1. A **web** területen válassza az **URI hozzáadása** hivatkozást, írja be a nevet `https://localhost:44316` , majd kattintson a **Mentés** gombra.
1. Válassza az **Áttekintés** lehetőséget.
1. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** , hogy a webalkalmazás konfigurálásakor egy későbbi lépésben használhassa.

#### <a name="applications-legacy"></a>[Alkalmazások (örökölt)](#tab/applications-legacy/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **alkalmazások (örökölt)** lehetőséget, majd válassza ki a *webapp1* alkalmazást.
1. A **Válasz URL-cím** területen adja hozzá a címet `https://localhost:44316` .
1. Kattintson a **Mentés** gombra.
1. A Tulajdonságok lapon jegyezze fel az alkalmazás AZONOSÍTÓját a webalkalmazás konfigurálásakor egy későbbi lépésben való használatra.

* * *

### <a name="create-a-client-secret"></a>Ügyfél titkos kulcsának létrehozása

Ezután hozzon létre egy ügyfél-titkot a regisztrált webalkalmazáshoz. A webalkalmazás kódjának mintája a következőt használja az identitás igazolására a jogkivonatok kérésekor.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>A minta konfigurálása

Ebben az oktatóanyagban egy olyan mintát konfigurál, amelyet letöltheti a GitHubról. A minta a ASP.NET használatával egyszerű feladatlistát biztosít. A minta a [Microsoft OWIN köztes összetevőket](/aspnet/aspnet/overview/owin-and-katana/)használja. [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip), vagy a klónozza a GitHubon található mintát. A mintafájlt egy olyan mappába kell kibontani, ahol a teljes elérési út rövidebb, mint 260 karakter.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

A következő két projekt szerepel a minta megoldásban:

* **TaskWebApp** – Feladatlista létrehozása és szerkesztése. A minta a **regisztrációs vagy bejelentkezési** felhasználói folyamat használatával regisztrálja és bejelentkezik a felhasználók számára.
* **TaskService** – támogatja a Feladatlista létrehozását, olvasását, frissítését és törlését. Az API-t a Azure AD B2C és a TaskWebApp által védettnek nevezzük.

A mintát úgy változtathatja meg, hogy a bérlőben regisztrált alkalmazást használja, beleértve az alkalmazás AZONOSÍTÓját és a korábban rögzített kulcsot is. A létrehozott felhasználói folyamatokat is konfigurálhatja. A minta a konfigurációs értékeket a *Web.config* fájlban megadott beállításokként határozza meg.

Frissítse a Web.config fájl beállításait a felhasználói folyamattal való együttműködéshez:

1. Nyissa meg a **B2C-WebAPI-DotNet** megoldást a Visual Studióban.
1. A **TaskWebApp** projektben nyissa meg a **Web.config** fájlt.
    1. Frissítse a és a értékét a `ida:Tenant` `ida:AadInstance` létrehozott Azure ad B2C bérlő nevével. Például cserélje le a következőt: `fabrikamb2c` `contoso` .
    1. Cserélje le a értéket a `ida:TenantId` címtár-azonosítóra, amelyet az Azure B2C-bérlő tulajdonságainál talál (a Azure Portal a **Azure Active Directory**  >  **Tulajdonságok**  >  **könyvtár azonosítója** alatt).
    1. Cserélje le a értékét `ida:ClientId` a rögzített alkalmazás-azonosítóra.
    1. Cserélje le az `ida:ClientSecret` értékét a feljegyzett kulcsra. Ha az ügyfél titkos kulcsa bármely előre definiált XML-entitást tartalmaz, például kisebb, mint ( `<` ), nagyobb, mint (), vagy a `>` `&` Double quote ( `"` ), akkor el kell kerülnie ezeket a karaktereket az ügyfél titkos kódjának a Web.configba való hozzáadása előtt.
    1. Cserélje le a értékét a értékre `ida:SignUpSignInPolicyId` `b2c_1_signupsignin1` .
    1. Cserélje le a értékét a értékre `ida:EditProfilePolicyId` `b2c_1_profileediting1` .
    1. Cserélje le a értékét a értékre `ida:ResetPasswordPolicyId` `b2c_1_passwordreset1` .

## <a name="run-the-sample"></a>Minta futtatása

1. Megoldáskezelő kattintson a jobb gombbal a **TaskWebApp** projektre, majd kattintson a **beállítás indítási projektként** lehetőségre.
1. Nyomja le az **F5** billentyűt. Az alapértelmezett böngésző elindítja a helyi webhely címét: `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

1. Válassza a **regisztráció/bejelentkezés** lehetőséget az alkalmazás felhasználójának való regisztrációhoz. A rendszer a **b2c_1_signupsignin1** felhasználói folyamatot használja.
1. Az Azure AD B2C megjelenít egy bejelentkezési oldalt egy regisztrációs hivatkozással. Mivel még nem rendelkezik fiókkal, válassza a **regisztráció most** lehetőséget. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A regisztrációs munkafolyamat a felhasználó jelszavát és a felhasználói folyamatban definiált kért attribútumokat is gyűjti.
1. Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket.

    ![A bejelentkezési vagy bejelentkezési munkafolyamat részeként megjelenő regisztrációs oldal](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. A **Létrehozás** gombra kattintva helyi fiókot hozhat létre a Azure ad B2C bérlőben.

Az alkalmazás felhasználója mostantól az e-mail-címük használatával bejelentkezhet, és használhatja a webalkalmazást.

A **Feladatlista** funkció azonban addig nem fog működni, amíg el nem végzi a következő oktatóanyagot a sorozatban, [oktatóanyag: Azure AD B2C használata a ASP.net webes API-k elleni védelemhez](tutorial-web-api-dotnet.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az alkalmazás frissítése Azure AD B2C
> * A minta beállítása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

Most lépjen a következő oktatóanyagra a webalkalmazás **Feladatlista** funkciójának engedélyezéséhez. Ebben az esetben egy webes API-alkalmazást kell regisztrálnia a saját Azure AD B2C bérlőben, majd módosítania kell a kódot, hogy a bérlőt használja az API-hitelesítéshez.

> [!div class="nextstepaction"]
> [Oktatóanyag: a ASP.NET webes API-k elleni védelem Azure Active Directory B2C használata >](tutorial-web-api-dotnet.md)