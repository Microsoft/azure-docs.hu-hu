---
title: 'Gyors útmutató: OIDC-alapú egyszeri bejelentkezés (SSO) beállítása egy alkalmazáshoz a Azure Active Directory (Azure AD) bérlőben'
description: Ez a rövid útmutató végigvezeti a OIDC-alapú egyszeri bejelentkezés (SSO) beállításának folyamatán a Azure Active Directory (Azure AD) bérlőben lévő alkalmazásokhoz.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: e5695d5ded92e9bb41d39a082790cfbb456dbc38
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258354"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Gyors útmutató: OIDC-alapú egyszeri bejelentkezés (SSO) beállítása egy alkalmazáshoz a Azure Active Directory (Azure AD) bérlőben

A Azure Active Directory (Azure AD) bérlőhöz hozzáadott alkalmazás egyszeri bejelentkezésének (SSO) beállításával megkezdheti az egyszerűsített felhasználói bejelentkezések használatát. Miután beállította az egyszeri bejelentkezést, a felhasználók Azure AD-beli hitelesítő adataikkal jelentkezhetnek be egy alkalmazásba. Az egyszeri bejelentkezést az Azure AD ingyenes kiadása tartalmazza.

További információ a OIDC-alapú egyszeri bejelentkezésről: a [OIDC-alapú egyszeri bejelentkezés megismerése](configure-oidc-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-bérlőhöz hozzáadott alkalmazás egyszeri bejelentkezésének beállításához a következők szükségesek:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A következő szerepkörök egyike: globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás rendszergazdája vagy az egyszerű szolgáltatásnév tulajdonosa.
- Egy olyan alkalmazás, amely támogatja az egyszeri bejelentkezést, és már előre konfigurálták, és hozzá lett adva az Azure AD-gyűjteményhez. A legtöbb alkalmazás használhatja az Azure AD-t az egyszeri bejelentkezéshez. Az Azure AD-katalógusban található alkalmazások előre konfigurálva vannak. Ha az alkalmazás nem szerepel a listáján, vagy egy egyéni fejlesztésű alkalmazás, akkor továbbra is használhatja az Azure AD-vel. Tekintse meg az oktatóanyagokat és az egyéb dokumentációt a tartalomjegyzékben. Ez a rövid útmutató az egyszeri bejelentkezéshez előre konfigurált, és az alkalmazások fejlesztői által az Azure AD-katalógusba való felvételre összpontosít.
- Nem kötelező: az [alkalmazások megtekintésének](view-applications-portal.md)befejezése.
- Nem kötelező: az [alkalmazás hozzáadásának](add-application-portal.md)befejezése.
- Nem kötelező: [alkalmazás konfigurálásának](add-application-portal-configure.md)befejezése.
- Nem kötelező: a [felhasználók hozzárendelésének befejezése egy alkalmazáshoz](add-application-portal-assign-users.md).

>[!IMPORTANT]
>A rövid útmutató lépéseinek teszteléséhez használjon nem éles környezetet.

## <a name="enable-single-sign-on-for-an-app"></a>Egyszeri bejelentkezés engedélyezése egy alkalmazáshoz

Ha olyan alkalmazást ad hozzá, amely a OIDC standard for SSO-t használja, akkor a telepítő gombja van. Amikor kiválasztja a gombot, lépjen az alkalmazások webhelyre, és fejezze be az alkalmazás regisztrációs folyamatát. Az alkalmazás hozzáadásának folyamatát az ebben a sorozatban korábban elérhető alkalmazás hozzáadása című rövid útmutatóban tárgyaljuk. Ha már hozzáadott alkalmazást konfigurál, tekintse meg az első rövid útmutatót. Végigvezeti Önt a bérlőn már meglévő alkalmazások megtekintésén. 

Egyszeri bejelentkezés beállítása egy alkalmazáshoz:

1. A sorozat korábbi rövid útmutatójában megtanulta, hogyan adhat hozzá egy alkalmazást, amely az Azure AD-bérlőt fogja használni az Identitáskezelés során. Ha az alkalmazás fejlesztője a OIDC standardot használta az SSO megvalósításához, akkor az alkalmazás hozzáadásakor megjelenik egy regisztrációs gomb. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="A képernyőképen az egyszeri bejelentkezési lehetőség és a regisztrációs gomb látható." lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. Válassza a **regisztráció** lehetőséget, és az alkalmazás fejlesztői bejelentkezési oldalára kerül sor. Jelentkezzen be Azure Active Directory bejelentkezési hitelesítő adataival. 

   > [!IMPORTANT]
    > Ha már rendelkezik előfizetéssel az alkalmazáshoz, akkor a felhasználói adatok érvényesítése és a bérlő/könyvtár adatai történnek. Ha az alkalmazás nem tudja ellenőrizni a felhasználót, akkor a rendszer átirányítja Önt az Application Service vagy a hiba oldalára.

3. A sikeres hitelesítés után egy párbeszédpanel jelenik meg, amely a rendszergazdai jóváhagyást kéri. Válassza **a jóváhagyva lehetőséget a szervezet nevében** , majd válassza az **elfogadás** lehetőséget. A felhasználói és rendszergazdai engedélyekkel kapcsolatos további információkért lásd: [felhasználói és rendszergazdai jogosultságok megismerése](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="Képernyőfelvétel: az alkalmazás beleegyezési képernyője." lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. A rendszer hozzáadja az alkalmazást a bérlőhöz, és megjelenik az alkalmazás kezdőlapja.


> [!TIP]
> Az Graph API segítségével automatizálhatja az alkalmazások felügyeletét: az [alkalmazások kezelésének automatizálása Microsoft Graph API-val](/graph/application-saml-sso-configure-api).

Itt talál egy videót a OIDC-alapú alkalmazások Azure AD-hez való hozzáadásával kapcsolatos további részletekért.

OIDC-alapú alkalmazás hozzáadása Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a rövid útmutatóval, érdemes lehet törölni az alkalmazást a tesztelési bérlő tisztításához. Az alkalmazás törlését a sorozat utolsó rövid útmutatója tartalmazza. [alkalmazás törlése](delete-application-portal.md).

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan törölhet egy alkalmazást.
> [!div class="nextstepaction"]
> [Alkalmazás törlése](delete-application-portal.md)
