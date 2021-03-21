---
title: 'Gyors útmutató: felhasználók kiosztása Azure Active Directory identitás-szolgáltatóként használó alkalmazáshoz'
description: Ez a rövid útmutató bemutatja, hogyan engedélyezheti a felhasználók számára, hogy az Azure AD identitás-szolgáltatóként való használatára beállított alkalmazást használjanak.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: kenwith
ms.openlocfilehash: 53dd2d15565149c3a9888ba063a6194ae033d8e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258371"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>Gyors útmutató: felhasználók kiosztása az Azure AD-t használó alkalmazásokhoz identitás-szolgáltatóként

Az előző rövid útmutatóban konfigurálta az alkalmazások tulajdonságait. A tulajdonságok beállításakor a rendszer a hozzárendelt és a hozzá nem rendelt felhasználókhoz tartozó felhasználói élményt is beállította. Ez a rövid útmutató végigvezeti a felhasználók az alkalmazáshoz való hozzárendelésének folyamatán.

## <a name="prerequisites"></a>Előfeltételek

Ha felhasználókat szeretne hozzárendelni az Azure AD-bérlőhöz hozzáadott alkalmazáshoz, a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A következő szerepkörök egyike: globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás rendszergazdája vagy az egyszerű szolgáltatásnév tulajdonosa.
- Nem kötelező: az [alkalmazások megtekintésének](view-applications-portal.md)befejezése.
- Nem kötelező: az [alkalmazás hozzáadásának](add-application-portal.md)befejezése.
- Nem kötelező: [alkalmazás konfigurálásának](add-application-portal-configure.md)befejezése.

>[!IMPORTANT]
>A rövid útmutató lépéseinek teszteléséhez használjon nem éles környezetet.

## <a name="assign-users-to-an-app"></a>Felhasználók hozzárendelése egy alkalmazáshoz
1. Az Azure AD-portálon válassza a **vállalati alkalmazások** lehetőséget. Ezután keresse meg és válassza ki a konfigurálni kívánt alkalmazást.
2. A bal oldali navigációs menüben válassza a **felhasználók és csoportok** lehetőséget.
   > [!NOTE]
   > Néhány Microsoft 365 alkalmazás a PowerShell használatát igényli. 
3. Válassza a **felhasználó hozzáadása** gombot.
4. A **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok** lehetőséget.
5. Válassza ki azt a felhasználót vagy csoportot, amelyet hozzá szeretne rendelni az alkalmazáshoz. A keresőmezőbe írja be a felhasználó vagy a csoport nevét is. Több felhasználót és csoportot is kiválaszthat, és a **kiválasztott elemek** területen megjelennek a kiválasztások.
    > [!IMPORTANT]
    > Ha egy alkalmazáshoz rendel hozzá egy csoportot, akkor csak a csoport felhasználói férhetnek hozzá. A hozzárendelés nem kaszkádba ágyazott csoportokba kerül.

    > [!NOTE]
    > A csoport alapú hozzárendeléshez prémium szintű Azure Active Directory P1 vagy P2 kiadás szükséges. A csoport alapú hozzárendelés csak biztonsági csoportok esetén támogatott. A beágyazott csoporttagságok és Microsoft 365 csoportok jelenleg nem támogatottak. A cikkben tárgyalt funkciókra vonatkozó további licencelési követelményekért tekintse meg a [Azure Active Directory díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/active-directory). 
6. Ha elkészült, válassza a **kiválasztás** lehetőséget.
   ![Felhasználó vagy csoport társítása az alkalmazáshoz](./media/assign-user-or-group-access-portal/assign-users.png)
7. A **felhasználók és csoportok** panelen válasszon ki egy vagy több felhasználót vagy csoportot a listából, majd kattintson a panel alján található **kiválasztás** gombra.
8. Ha az alkalmazás támogatja azt, hozzárendelhet egy szerepkört a felhasználóhoz vagy a csoportjához. A **hozzárendelés hozzáadása** panelen válassza a **szerepkör kiválasztása** lehetőséget. Ezután a **szerepkör kiválasztása** panelen válassza ki a kiválasztott felhasználókra vagy csoportokra alkalmazni kívánt szerepkört, majd kattintson az **OK gombra** a panel alján. 
    > [!NOTE]
    > Ha az alkalmazás nem támogatja a szerepkör kiválasztását, a rendszer az alapértelmezett hozzáférési szerepkört rendeli hozzá. Ebben az esetben az alkalmazás kezeli a felhasználók hozzáférési szintjét.
9. A **hozzárendelés hozzáadása** panelen kattintson a **hozzárendelés** gombra a panel alján.

A felhasználókat és csoportokat ugyanazzal az eljárással lehet kiosztani. Válassza ki azt a felhasználót vagy csoportot, amelynek hozzárendelését törölni szeretné, majd válassza az **Eltávolítás** lehetőséget. A Microsoft 365 és az Office 365-alkalmazások némelyike a PowerShell használatát igényli. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a gyors üzembe helyezéssel, vegye fontolóra az alkalmazás törlését. Így megőrizheti a teszt bérlőjét. Az alkalmazás törlését a sorozat utolsó rövid útmutatója tartalmazza. [alkalmazás törlése](delete-application-portal.md).

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan állíthatja be az egyszeri bejelentkezést az alkalmazásokhoz.
> [!div class="nextstepaction"]
> [SAML-alapú egyszeri bejelentkezés beállítása](add-application-portal-setup-sso.md)

OR

> [!div class="nextstepaction"]
> [OIDC-alapú egyszeri bejelentkezés beállítása](add-application-portal-setup-oidc-sso.md)
