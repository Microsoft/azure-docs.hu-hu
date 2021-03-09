---
title: 'Gyors útmutató: SAML-alapú egyszeri bejelentkezés (SSO) beállítása egy alkalmazáshoz a Azure Active Directory (Azure AD) bérlőben'
description: Ez a rövid útmutató végigvezeti az SAML-alapú egyszeri bejelentkezés (SSO) beállításának folyamatán a Azure Active Directory (Azure AD) bérlőben lévő alkalmazásokhoz.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 44a838bdce4721d24bd0bdbb45ac7b36ceb34e71
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488369"
---
# <a name="quickstart-set-up-saml-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Gyors útmutató: SAML-alapú egyszeri bejelentkezés (SSO) beállítása egy alkalmazáshoz a Azure Active Directory (Azure AD) bérlőben

A Azure Active Directory (Azure AD) bérlőhöz hozzáadott alkalmazás egyszeri bejelentkezésének (SSO) beállításával megkezdheti az egyszerűsített felhasználói bejelentkezések használatát. Miután beállította az egyszeri bejelentkezést, a felhasználók Azure AD-beli hitelesítő adataikkal jelentkezhetnek be egy alkalmazásba. Az egyszeri bejelentkezést az Azure AD ingyenes kiadása tartalmazza.

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

Miután befejezte az alkalmazás hozzáadását az Azure AD-bérlőhöz, megjelenik az Áttekintés oldal. Ha már hozzáadott alkalmazást konfigurál, tekintse meg az első rövid útmutatót. Végigvezeti Önt a bérlőhöz hozzáadott alkalmazások megtekintésén.

Egyszeri bejelentkezés beállítása egy alkalmazáshoz:

1. Az Azure AD-portálon válassza a **vállalati alkalmazások** lehetőséget. Ezután keresse meg és válassza ki azt az alkalmazást, amelyet be szeretne állítani az egyszeri bejelentkezéshez.
1. A **kezelés** szakaszban válassza az **egyszeri bejelentkezés** lehetőséget az **egyszeri bejelentkezési** ablaktábla szerkesztéshez való megnyitásához.

    > [!IMPORTANT]
    > Ha az alkalmazás az egyszeri bejelentkezés OpenID Connect (OIDC) szabványát használja, akkor a navigációban nem jelenik meg egyszeri bejelentkezés lehetőség. Tekintse meg a OIDC-alapú egyszeri bejelentkezésről szóló rövid útmutatót, amelyből megtudhatja, hogyan állíthatja be.

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="A képernyőképen az Azure AD portál egyszeri bejelentkezési konfiguráció lapja látható.":::

1. Válassza az **SAML** lehetőséget az SSO-konfiguráció lap megnyitásához. Ebben a példában az egyszeri bejelentkezéshez konfigurált alkalmazás GitHub. Miután beállította a GitHubot, a felhasználók bejelentkezhetnek a GitHubra az Azure AD-bérlőben lévő hitelesítő adataik használatával.

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Képernyőfelvétel: az egyszeri bejelentkezési konfiguráció oldal a GitHubon.":::

1. Az Azure AD az SAML-alapú SSO használatára való konfigurálásának folyamata az alkalmazástól függően változhat. Van egy hivatkozás a GitHub útmutatására. Az egyéb alkalmazásokhoz kapcsolódó útmutatókért lásd: [útmutatók az SaaS-alkalmazások integrálásához a Azure Active Directory](/azure/active-directory/saas-apps/)használatával.
1. Az alkalmazáshoz tartozó egyszeri bejelentkezés beállításához kövesse az útmutatót. Számos alkalmazás rendelkezik konkrét előfizetési követelményekkel az egyszeri bejelentkezés működéséhez. A GitHub például nagyvállalati előfizetést igényel.
    > [!TIP]
    > További információ az SAML konfigurációs lehetőségeiről: [SAML-alapú egyszeri bejelentkezés konfigurálása](configure-saml-single-sign-on.md).

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Képernyőfelvétel: az egyszeri bejelentkezés lehetősége a GitHub díjszabási oldalának vállalati előfizetésében.":::

> [!TIP]
> Az Graph API segítségével automatizálhatja az alkalmazások felügyeletét: az [alkalmazások kezelésének automatizálása Microsoft Graph API-val](/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a rövid útmutatóval, érdemes lehet törölni az alkalmazást a tesztelési bérlő tisztításához. Az alkalmazás törlését a sorozat utolsó rövid útmutatója tartalmazza. [alkalmazás törlése](delete-application-portal.md).

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan törölhet egy alkalmazást.
> [!div class="nextstepaction"]
> [Alkalmazás törlése](delete-application-portal.md)
