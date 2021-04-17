---
title: 'Rövid útmutató: Felhasználók hozzárendelése egy olyan alkalmazáshoz, amely Azure Active Directory identitásszolgáltatóként használ'
description: Ez a rövid útmutató végigvezeti azon a folyamaton, amely lehetővé teszi a felhasználók számára egy olyan alkalmazás használatát, amely az Azure AD identitásszolgáltatóként való használatára van beállítva.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: iangithinji
ms.openlocfilehash: eb6797fdfb57a503b6dece9f2dfdc3cb0c67feef
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375338"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>Rövid útmutató: Felhasználók hozzárendelése egy olyan alkalmazáshoz, amely identitásszolgáltatóként használja az Azure AD-t

Az előző rövid útmutatóban konfigurálta egy alkalmazás tulajdonságait. A tulajdonságok beállításakor a felhasználói élményt a hozzárendelt és a nem hozzárendelt felhasználók számára is konfigurálta. Ez a rövid útmutató végigvezeti a felhasználók alkalmazáshoz való hozzárendelésének folyamatán.

## <a name="prerequisites"></a>Előfeltételek

Ha felhasználókat szeretne hozzárendelni egy, az Azure AD-bérlőhöz hozzáadott alkalmazáshoz, a következőre lesz szüksége:

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- A következő szerepkörök egyike: globális rendszergazda, felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda vagy a szolgáltatásnév tulajdonosa.
- Nem kötelező: Az alkalmazások [megtekintése befejeztével.](view-applications-portal.md)
- Nem kötelező: Az [Alkalmazás hozzáadása elem befejezése.](add-application-portal.md)
- Nem kötelező: Az alkalmazás [konfigurálása befejeztével.](add-application-portal-configure.md)

>[!IMPORTANT]
>A rövid útmutató lépéseit nem éles környezetben tesztelje.

## <a name="assign-users-to-an-app"></a>Felhasználók hozzárendelése egy alkalmazáshoz
1. Az Azure AD portálon válassza a Vállalati **alkalmazások lehetőséget.** Ezután keresse meg és válassza ki a konfigurálni kívánt alkalmazást.
2. A bal oldali navigációs menüben válassza a **Felhasználók és csoportok lehetőséget.**
   > [!NOTE]
   > Néhány alkalmazáshoz Microsoft 365 PowerShell használata szükséges. 
3. Válassza a **Felhasználó hozzáadása** gombot.
4. A Hozzárendelés **hozzáadása panelen** válassza a **Felhasználók és csoportok lehetőséget.**
5. Válassza ki az alkalmazáshoz hozzárendelni kívánt felhasználót vagy csoportot. A keresőmezőbe a felhasználó vagy csoport nevét is beírhatja. Több felhasználót és csoportot is kiválaszthat, és a kijelölések a Kijelölt elemek **alatt jelennek meg.**
    > [!IMPORTANT]
    > Amikor csoportot rendel egy alkalmazáshoz, csak a csoport felhasználói férhetnek hozzá. A hozzárendelés nem kaszkádolt beágyazott csoportokhoz.

    > [!NOTE]
    > A csoportalapú hozzárendeléshez prémium szintű Azure Active Directory P1 vagy P2 kiadás szükséges. A csoportalapú hozzárendelés csak biztonsági csoportok esetén támogatott. A beágyazott csoporttagság és Microsoft 365 csoport jelenleg nem támogatott. A cikkben tárgyalt szolgáltatások licencelési követelményeivel kapcsolatos további információkért lásd a Azure Active Directory [oldalon.](https://azure.microsoft.com/pricing/details/active-directory) 
6. Ha elkészült, válassza a **Kijelölés lehetőséget.**
   ![Felhasználó vagy csoport hozzárendelése az alkalmazáshoz](./media/assign-user-or-group-access-portal/assign-users.png)
7. A Felhasználók **és csoportok panelen** válasszon ki egy vagy több  felhasználót vagy csoportot a listából, majd kattintson a panel alján található Kijelölés gombra.
8. Ha az alkalmazás támogatja, hozzárendelhet egy szerepkört a felhasználóhoz vagy csoporthoz. A Hozzárendelés **hozzáadása panelen** válassza a **Szerepkör kiválasztása lehetőséget.** Ezután a **Szerepkör** kiválasztása panelen válassza ki a kiválasztott felhasználókra vagy csoportokra alkalmazni kívánt szerepkört, majd kattintson a panel alján található **OK** gombra. 
    > [!NOTE]
    > Ha az alkalmazás nem támogatja a szerepkör kiválasztását, a rendszer hozzárendeli az alapértelmezett hozzáférési szerepkört. Ebben az esetben az alkalmazás kezeli a felhasználók hozzáférési szintjét.
9. A Hozzárendelés **hozzáadása panelen** válassza a hozzárendelés gombot a panel alján. 

A felhasználók vagy csoportok hozzárendelését ugyanaz az eljárás segítségével lehet megszülni. Válassza ki a törölni kívánt felhasználót vagy csoportot, majd válassza az Eltávolítás **lehetőséget.** Egyes Microsoft 365 office 365-alkalmazásokhoz a PowerShell használata szükséges. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a rövid útmutatóval, érdemes törölnie az alkalmazást. Így tisztán tarthatja a tesztbérlőt. Az alkalmazás törlését a sorozat utolsó rövid útmutatója fedi le. Lásd: [Alkalmazás törlése.](delete-application-portal.md)

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan állíthat be egyszeri bejelentkezést egy alkalmazáshoz.
> [!div class="nextstepaction"]
> [SAML-alapú egyszeri bejelentkezés beállítása](add-application-portal-setup-sso.md)

OR

> [!div class="nextstepaction"]
> [OIDC-alapú egyszeri bejelentkezés beállítása](add-application-portal-setup-oidc-sso.md)
