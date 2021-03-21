---
title: 'Gyors útmutató: alkalmazás tulajdonságainak konfigurálása a Azure Active Directory (Azure AD) bérlőben'
description: Ez a rövid útmutató a Azure Portal használatával konfigurálja az Azure Active Directory (Azure AD) Bérlővel regisztrált alkalmazást.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: e83caba2d2a5435c5cdac02c6f63094dc079c43c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258608"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Gyors útmutató: alkalmazás tulajdonságainak konfigurálása a Azure Active Directory (Azure AD) bérlőben

Az előző rövid útmutatóban egy alkalmazást adott hozzá a Azure Active Directory (Azure AD) bérlőhöz. Ha hozzáad egy alkalmazást, az Azure AD-bérlőnek tudnia kell, hogy az alkalmazás identitás-szolgáltatója. Most konfigurálja az alkalmazás egyes tulajdonságait.
 
## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-bérlőben található alkalmazások tulajdonságainak konfigurálásához a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A következő szerepkörök egyike: globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás rendszergazdája vagy az egyszerű szolgáltatásnév tulajdonosa.
- Nem kötelező: az [alkalmazások megtekintésének](view-applications-portal.md)befejezése.
- Nem kötelező: az [alkalmazás hozzáadásának](add-application-portal.md)befejezése.

>[!IMPORTANT]
>A rövid útmutató lépéseinek teszteléséhez használjon nem éles környezetet.

## <a name="configure-app-properties"></a>Alkalmazás tulajdonságainak konfigurálása

Amikor befejezi az alkalmazás hozzáadását az Azure AD-bérlőhöz, megjelenik az Áttekintés oldal. Ha már hozzáadott alkalmazást konfigurál, tekintse meg az első rövid útmutatót. Végigvezeti Önt a bérlőhöz hozzáadott alkalmazások megtekintésén. 

Az alkalmazás tulajdonságainak szerkesztése:

1. Az Azure AD-portálon válassza a **vállalati alkalmazások** lehetőséget. Ezután keresse meg és válassza ki a konfigurálni kívánt alkalmazást.
2. A **kezelés** szakaszban válassza a **Tulajdonságok** lehetőséget a **Tulajdonságok** ablaktábla szerkesztéshez való megnyitásához.
3. Szánjon egy kis időt az elérhető lehetőségek megismerésére. Az elérhető lehetőségek attól függnek, hogy az alkalmazás hogyan integrálódik az Azure AD-vel. Az SAML-alapú egyszeri bejelentkezést használó alkalmazások például olyan mezőkkel rendelkeznek, mint például a *felhasználói hozzáférési URL-cím* , míg egy OIDC-alapú egyszeri bejelentkezést használó alkalmazás nem fog megjelenni. Azt is vegye figyelembe, hogy a **Azure Active Directory > Alkalmazásregisztrációk** használatával hozzáadott alkalmazások alapértelmezett OIDC-alapú alkalmazások. Míg a **Azure Active Directory > vállalati alkalmazások** által hozzáadott alkalmazások számos egyszeri bejelentkezési szabványt használhatnak. Minden alkalmazás tartalmazni fog mezőket a konfiguráláshoz, amikor megjelenik egy alkalmazás és használható. Ezek a mezők a következők:
    - **Engedélyezve van a felhasználók számára a bejelentkezés?** meghatározza, hogy be tud-e jelentkezni az alkalmazáshoz hozzárendelt felhasználók.
    - **Felhasználói hozzárendelés szükséges?** meghatározza, hogy be tud-e jelentkezni az alkalmazáshoz nem rendelt felhasználók.
    - **Látható a felhasználók számára?** meghatározza, hogy az alkalmazáshoz rendelt felhasználók láthatják-e az alkalmazásokban [és Microsoft 365 az App](https://myapps.microsoft.com) launcherben. (Lásd a Microsoft 365 webhely bal felső sarkában található Waffle menüt.)
    
    > [!TIP]
    > A felhasználók kiosztása a navigáció **felhasználók és csoportok** szakaszában történik.

    A három lehetőség egymástól függetlenül válthatók, és az eredményül kapott viselkedés nem mindig nyilvánvaló. Itt látható egy táblázat, amely segítségére lehet:
    
    | Engedélyezve van a felhasználók számára a bejelentkezés? | Felhasználó-hozzárendelés szükséges? | Felhasználók számára látható? | Az alkalmazáshoz rendelt vagy nem használt felhasználók viselkedése. |
    |---|---|---|---|
    | Igen | Igen | Yes | A hozzárendelt felhasználók láthatják az alkalmazást, és bejelentkezhetnek.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be. |
    | Igen | Igen | Nem  | A hozzárendelt felhasználások nem látják az alkalmazást, de be tudnak jelentkezni.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be. |
    | Igen | Nem  | Igen | A hozzárendelt felhasználók láthatják az alkalmazást, és bejelentkezhetnek.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, de jelentkezhetnek be. |
    | Igen | Nem  | Nem  | A hozzárendelt felhasználók nem látják az alkalmazást, de be tudnak jelentkezni.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, de jelentkezhetnek be. |
    | Nem  | Igen | Yes | A hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be. |
    | Nem  | Igen | Nem  | A hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be. |
    | Nem  | Nem  | Igen | A hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be. |
    | Nem  | Nem  | Nem  | A hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be. |

4. Ha elkészült, válassza a **Mentés** lehetőséget.

## <a name="use-a-custom-logo"></a>Egyéni embléma használata

Egyéni embléma használata:

1. Hozzon létre egy 215-es, 215 képpont-os emblémát, és mentse. png formátumban.
2. Az Azure AD-portálon válassza a **vállalati alkalmazások** lehetőséget. Ezután keresse meg és válassza ki a konfigurálni kívánt alkalmazást.
3. A **kezelés** szakaszban válassza a **Tulajdonságok** lehetőséget a **Tulajdonságok** ablaktábla szerkesztéshez való megnyitásához. 
4. Válassza ki az ikont az embléma feltöltéséhez.
5. Ha elkészült, válassza a **Mentés** lehetőséget.

    ![Képernyőkép a tulajdonságok képernyőről, amely bemutatja, hogyan módosíthatja az emblémát.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > A **Tulajdonságok** ablaktáblán megjelenő miniatűr nem azonnal frissül. A frissített ikon megjelenítéséhez kattintson a **Tulajdonságok** panel bezárásához és újbóli megnyitásához.


> [!TIP]
> Az Graph API segítségével automatizálhatja az alkalmazások felügyeletét: az [alkalmazások kezelésének automatizálása Microsoft Graph API-val](/graph/application-saml-sso-configure-api).

## <a name="add-notes"></a>Megjegyzések hozzáadása

A megjegyzések mezővel az alkalmazás Azure AD-ben való kezeléséhez kapcsolódó információkat adhat hozzá. A Notes egy ingyenes szövegmező, amelynek maximális mérete 1024 karakter.

1. Az Azure AD-portálon válassza a **vállalati alkalmazások** lehetőséget. Ezután keresse meg és válassza ki a konfigurálni kívánt alkalmazást.
2. A **kezelés** szakaszban válassza a **Tulajdonságok** lehetőséget a **Tulajdonságok** ablaktábla szerkesztéshez való megnyitásához.
3. Frissítse a Megjegyzések mezőt, majd válassza a **Mentés** lehetőséget.

    ![Képernyőfelvétel a tulajdonságok képernyőről, amely bemutatja, hogyan módosíthatja a megjegyzéseket](media/add-application-portal/notes-application.png)

    
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a gyors üzembe helyezési sorozatot, akkor érdemes lehet törölni az alkalmazást a tesztelési bérlő törléséhez. Az alkalmazás törlését a sorozat utolsó rövid útmutatója tartalmazza. [alkalmazás törlése](delete-application-portal.md).

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan rendelhet hozzá felhasználókat az alkalmazáshoz.
> [!div class="nextstepaction"]
> [Felhasználók hozzárendelése egy alkalmazáshoz](add-application-portal-assign-users.md)