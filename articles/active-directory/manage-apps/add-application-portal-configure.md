---
title: 'Rövid útmutató: Tulajdonságok konfigurálása egy alkalmazáshoz a Azure Active Directory (Azure AD-) bérlőben'
description: Ez a rövid útmutató Azure Portal segítségével konfigurál egy alkalmazást, amely regisztrálva van a Azure Active Directory (Azure AD)-bérlőben.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: iangithinji
ms.openlocfilehash: 3b7a5d88aa40422dc46c6ca2c1681447cb030ea4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379519"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Rövid útmutató: Tulajdonságok konfigurálása egy alkalmazáshoz a Azure Active Directory (Azure AD-) bérlőben

Az előző rövid útmutatóban hozzáadott egy alkalmazást a Azure Active Directory (Azure AD) bérlőjéhez. Alkalmazás hozzáadásakor tudatja az Azure AD-bérlővel, hogy ez az alkalmazás identitásszolgáltatója. Most konfigurálja az alkalmazás néhány tulajdonságát.
 
## <a name="prerequisites"></a>Előfeltételek

Egy alkalmazás tulajdonságainak konfigurálához az Azure AD-bérlőben a következőre van szükség:

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- A következő szerepkörök egyike: globális rendszergazda, felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda vagy a szolgáltatásnév tulajdonosa.
- Nem kötelező: Az alkalmazások [megtekintése befejeztével.](view-applications-portal.md)
- Nem kötelező: Az [Alkalmazás hozzáadása elem befejezése.](add-application-portal.md)

>[!IMPORTANT]
>A rövid útmutató lépéseit nem éles környezetben tesztelje.

## <a name="configure-app-properties"></a>Alkalmazástulajdonságok konfigurálása

Amikor befejezte egy alkalmazás hozzáadását az Azure AD-bérlőhöz, megjelenik az áttekintő oldal. Ha már hozzáadott alkalmazást konfigurál, nézze meg az első rövid útmutatót. Végigvezeti a bérlőhöz hozzáadott alkalmazások megtekintésén. 

Az alkalmazás tulajdonságainak szerkesztése:

1. Az Azure AD portálon válassza a Vállalati **alkalmazások lehetőséget.** Ezután keresse meg és válassza ki a konfigurálni kívánt alkalmazást.
2. A Kezelés **szakaszban** válassza a **Tulajdonságok lehetőséget** a Tulajdonságok **panel szerkesztéshez** való megnyitásához.
3. Az elérhető lehetőségeket csak néhány pillanat alatt értheti meg. Az elérhető lehetőségek attól függnek, hogy az alkalmazás hogyan van integrálva az Azure AD-be. Az SAML-alapú egyszeri bejelentkezést használó alkalmazások például olyan  mezőket tartalmaznak, mint a Felhasználói hozzáférés URL-címe, míg az OIDC-alapú egyszeri bejelentkezést használó alkalmazások nem. Azt is vegye figyelembe, hogy **a** Azure Active Directory > Alkalmazásregisztrációk által hozzáadott alkalmazások alapértelmezés szerint OIDC-alapú alkalmazások. Míg a Azure Active Directory > **vállalati** alkalmazások számos egyszeri bejelentkezési szabványt használhatnak. Minden alkalmazás mezőket tartalmaz az alkalmazás megjelenésekor és használhatóként való konfigurálásához. Ezek a mezők a következők:
    - **Engedélyezve van a felhasználók számára a bejelentkezés?** meghatározza, hogy az alkalmazáshoz hozzárendelt felhasználók bejelentkeznek-e.
    - **Felhasználó-hozzárendelésre van szükség?** A meghatározza, hogy az alkalmazáshoz nem hozzárendelt felhasználók bejelentkeznek-e.
    - **Látható a felhasználók számára?** azt határozza meg, hogy az alkalmazáshoz hozzárendelt felhasználók [láthatják-Saját alkalmazások](https://myapps.microsoft.com) és Microsoft 365 appindítóban. (Lásd a gofri menüt egy webhely bal felső sarkában Microsoft 365.)
    
    > [!TIP]
    > A felhasználók hozzárendelése a navigáció Felhasználók **és** csoportok szakaszában történik.

    A három lehetőség egymástól függetlenül válthat, és az eredményül kapott viselkedés nem mindig nyilvánvaló. Az alábbi táblázat segíthet:
    
    | Engedélyezve van a felhasználók számára a bejelentkezés? | Felhasználó-hozzárendelés szükséges? | Felhasználók számára látható? | Az alkalmazáshoz rendelt vagy nem hozzárendelt felhasználók viselkedése. |
    |---|---|---|---|
    | Igen | Igen | Igen | A hozzárendelt felhasználók láthatják az alkalmazást, és bejelentkeznek.<br>A nem hozzárendelt felhasználók nem láthatják az alkalmazást, és nem tudnak bejelentkezni. |
    | Igen | Igen | Nem  | A hozzárendelt használati módok nem látják az alkalmazást, de be tudnak jelentkezni.<br>A nem hozzárendelt felhasználók nem láthatják az alkalmazást, és nem tudnak bejelentkezni. |
    | Igen | Nem  | Igen | A hozzárendelt felhasználók láthatják az alkalmazást, és bejelentkeznek.<br>A nem hozzárendelt felhasználók nem láthatják az alkalmazást, de be tudnak jelentkezni. |
    | Igen | Nem  | Nem  | A hozzárendelt felhasználók nem láthatják az alkalmazást, de be tudnak jelentkezni.<br>A nem hozzárendelt felhasználók nem láthatják az alkalmazást, de be tudnak jelentkezni. |
    | Nem  | Igen | Igen | A hozzárendelt felhasználók nem láthatják az alkalmazást, és nem tudnak bejelentkezni.<br>A nem hozzárendelt felhasználók nem láthatják az alkalmazást, és nem tudnak bejelentkezni. |
    | Nem  | Igen | Nem  | A hozzárendelt felhasználók nem láthatják az alkalmazást, és nem tudnak bejelentkezni.<br>A nem hozzárendelt felhasználók nem láthatják az alkalmazást, és nem tudnak bejelentkezni. |
    | Nem  | Nem  | Igen | A hozzárendelt felhasználók nem láthatják az alkalmazást, és nem tudnak bejelentkezni.<br>A nem hozzárendelt felhasználók nem láthatják az alkalmazást, és nem tudnak bejelentkezni. |
    | Nem  | Nem  | Nem  | A hozzárendelt felhasználók nem láthatják az alkalmazást, és nem tudnak bejelentkezni.<br>A nem hozzárendelt felhasználók nem láthatják az alkalmazást, és nem tudnak bejelentkezni. |

4. Ha elkészült, válassza a **Mentés lehetőséget.**

## <a name="use-a-custom-logo"></a>Egyéni embléma használata

Egyéni embléma használata:

1. Hozzon létre egy 215 by 215 képpontos emblémát, és mentse .png formátumban.
2. Az Azure AD portálon válassza a Vállalati **alkalmazások lehetőséget.** Ezután keresse meg és válassza ki a konfigurálni kívánt alkalmazást.
3. A Kezelés **szakaszban** válassza a **Tulajdonságok lehetőséget** a Tulajdonságok **panel szerkesztéshez** való megnyitásához. 
4. Kattintson az ikonra az embléma feltöltéshez.
5. Ha elkészült, válassza a **Mentés lehetőséget.**

    ![Az emblémaváltást bemutató Tulajdonságok képernyő képernyőképe.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > A Tulajdonságok panelen **megjelenő** miniatűr nem frissül azonnal. A frissített ikont  a Tulajdonságok panel bezárhatja, majd újra megnyithatja.


> [!TIP]
> Az alkalmazáskezelést a következő Graph API automatizálhatja: Az alkalmazáskezelés [automatizálása a Microsoft Graph API-val.](/graph/application-saml-sso-configure-api)

## <a name="add-notes"></a>Megjegyzések hozzáadása

A Megjegyzések mezőben az alkalmazás Azure AD-beli felügyeletéhez kapcsolódó összes információt hozzáadhatja. A Megjegyzések egy legfeljebb 1024 karakter hosszúságú szabad szövegmező.

1. Az Azure AD portálon válassza a Vállalati **alkalmazások lehetőséget.** Ezután keresse meg és válassza ki a konfigurálni kívánt alkalmazást.
2. A Kezelés **szakaszban** válassza a **Tulajdonságok lehetőséget** a Tulajdonságok **panel szerkesztéshez** való megnyitásához.
3. Frissítse a Megjegyzések mezőt, majd válassza a **Mentés lehetőséget.**

    ![Képernyőkép a Tulajdonságok képernyőről, amely bemutatja, hogyan módosíthatja a megjegyzéseket](media/add-application-portal/notes-application.png)

    
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a gyorsindítási sorozatot, érdemes törölnie az alkalmazást a tesztbérlő törléséhez. Az alkalmazás törlésével a sorozat utolsó rövid útmutatója az Alkalmazás törlése( ) [cikkből áll.](delete-application-portal.md)

## <a name="next-steps"></a>Következő lépések

A következő cikk azt is bemutatja, hogyan rendelhet hozzá felhasználókat az alkalmazáshoz.
> [!div class="nextstepaction"]
> [Felhasználók hozzárendelése egy alkalmazáshoz](add-application-portal-assign-users.md)