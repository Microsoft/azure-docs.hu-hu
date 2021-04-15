---
title: 'Rövid útmutató: SAML-alapú egyszeri bejelentkezés (SSO) beállítása egy alkalmazáshoz a Azure Active Directory (Azure AD) bérlőben'
description: Ez a rövid útmutató végigvezeti az SAML-alapú egyszeri bejelentkezés (SSO) beállításán az Azure Active Directory-bérlőben (Azure AD)-bérlőben.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: iangithinji
ms.openlocfilehash: 802cc744063ba2bf1110915ae9b22c9c00e09cfa
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378907"
---
# <a name="quickstart-set-up-saml-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Rövid útmutató: SAML-alapú egyszeri bejelentkezés (SSO) beállítása egy alkalmazáshoz a Azure Active Directory (Azure AD) bérlőben

Az egyszerűsített felhasználói bejelentkezések használatának első lépésekéhez egyszeri bejelentkezést (SSO) kell bejellni az Azure Active Directory- (Azure AD-) bérlőhöz hozzáadott alkalmazásokhoz. Az SSO beállítása után a felhasználók bejelentkeznek az alkalmazásba az Azure AD-beli hitelesítő adataik használatával. Az SSO az Azure AD ingyenes kiadásában található meg.

## <a name="prerequisites"></a>Előfeltételek

Az SSO beállításához az Azure AD-bérlőhöz hozzáadott alkalmazásokhoz a következő szükséges:

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- A következő szerepkörök egyike: globális rendszergazda, felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda vagy a szolgáltatásnév tulajdonosa.
- Olyan alkalmazás, amely támogatja az SSO-t, és amely már előre konfigurálva van, és hozzá lett adva az Azure AD-katalógushoz. A legtöbb alkalmazás használhatja az Azure AD-t az SSO-hoz. Az Azure AD-katalógusban található alkalmazások előre konfigurálva vannak. Ha az alkalmazás nem szerepel a listán, vagy egyénileg fejlesztett alkalmazás, akkor is használhatja az Azure AD-val. Tekintse meg a tartalomjegyzékben található oktatóanyagokat és egyéb dokumentációkat. Ez a rövid útmutató az SSO-hoz előre konfigurált és az alkalmazásfejlesztők által az Azure AD-katalógushoz hozzáadott alkalmazásokra összpontosít.
- Nem kötelező: Az alkalmazások [megtekintése befejeztével.](view-applications-portal.md)
- Nem kötelező: Az [Alkalmazás hozzáadása elem befejezése.](add-application-portal.md)
- Nem kötelező: Az alkalmazás [konfigurálása befejeztével.](add-application-portal-configure.md)
- Nem kötelező: A felhasználók [alkalmazáshoz való hozzárendelésének befejezése.](add-application-portal-assign-users.md)


>[!IMPORTANT]
>A rövid útmutató lépéseit nem éles környezetben tesztelje.

## <a name="enable-single-sign-on-for-an-app"></a>Egyszeri bejelentkezés engedélyezése egy alkalmazáshoz

Miután befejezte egy alkalmazás hozzáadását az Azure AD-bérlőhöz, megjelenik az áttekintő oldal. Ha már hozzáadott alkalmazást konfigurál, nézze meg az első rövid útmutatót. Ez végigvezeti a bérlőhöz hozzáadott alkalmazások megtekintésén.

Egyszeri bejelentkezés beállítása egy alkalmazáshoz:

1. Az Azure AD portálon válassza a Vállalati **alkalmazások lehetőséget.** Ezután keresse meg és válassza ki az egyszeri bejelentkezéshez beállítani kívánt alkalmazást.
1. A Kezelés **szakaszban** válassza az **Egyszeri bejelentkezés** lehetőséget az Egyszeri **bejelentkezés** panel szerkesztéshez való megnyitásához.

    > [!IMPORTANT]
    > Ha az alkalmazás az OpenID Connect (OIDC) szabványt használja az egyszeri bejelentkezéshez, akkor nem fog egyszeri bejelentkezési lehetőséget látni a navigációban. Ennek beállítását az OIDC-alapú SSO rövid útmutatója alapján sajátítsa el.

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Képernyőkép az Azure AD portál egyszeri bejelentkezés konfigurációs oldalának képernyőképe.":::

1. Válassza **az SAML** lehetőséget az SSO konfigurációs oldalának megnyitásához. Ebben a példában az SSO-ra konfigurált alkalmazás a GitHub. A GitHub beállítása után a felhasználók bejelentkeznek a GitHubba az Azure AD-bérlőből származó hitelesítő adataik használatával.

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Képernyőkép a GitHub Egyszeri bejelentkezés konfigurációs oldalának képernyőképe.":::

1. Az alkalmazás konfigurálása az Azure AD SAML-alapú SSO-hoz való használatára az alkalmazástól függően eltérő lehet. A GitHubhoz elérhető útmutatásra mutató hivatkozás. További útmutatók más alkalmazásokhoz: [Oktatóanyagok SaaS-alkalmazások](/azure/active-directory/saas-apps/)integrálásához a Azure Active Directory.
1. Kövesse az útmutatót az SSO beállításához az alkalmazáshoz. Számos alkalmazás rendelkezik egyedi előfizetési követelményekkel az SSO-funkciókhoz. A GitHubhoz például nagyvállalati előfizetés szükséges.
    > [!TIP]
    > Az SAML konfigurációs beállításaival kapcsolatos további információkért lásd: [SAML-alapú egyszeri bejelentkezés konfigurálása.](configure-saml-single-sign-on.md)

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Képernyőkép az Egyszeri bejelentkezés lehetőségről a GitHub díjszabási oldalának Vállalati előfizetésében.":::

> [!TIP]
> Az alkalmazáskezelést a következő Graph API automatizálhatja: Az alkalmazáskezelés [automatizálása a Microsoft Graph API-val.](/graph/application-saml-sso-configure-api)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett ezzel a rövid útmutató-sorozattal, érdemes törölnie az alkalmazást a tesztbérlő törléséhez. Az alkalmazás törlésével a sorozat utolsó rövid útmutatója az Alkalmazás törlése( ) [cikkből áll.](delete-application-portal.md)

## <a name="next-steps"></a>Következő lépések

A következő cikk azt is bemutatja, hogyan törölhet alkalmazásokat.
> [!div class="nextstepaction"]
> [Alkalmazás törlése](delete-application-portal.md)
