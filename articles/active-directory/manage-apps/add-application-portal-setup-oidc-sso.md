---
title: 'Rövid útmutató: OIDC-alapú egyszeri bejelentkezés (SSO) beállítása egy alkalmazáshoz a Azure Active Directory (Azure AD) bérlőben'
description: Ez a rövid útmutató végigvezeti az OIDC-alapú egyszeri bejelentkezés (SSO) beállításának folyamatán az Azure Active Directory-bérlőben (Azure AD)-bérlőben.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: iangithinji
ms.openlocfilehash: 2e0b49a73422a2f71af37e103e9d4fd6a18a1f61
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378941"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Rövid útmutató: OIDC-alapú egyszeri bejelentkezés (SSO) beállítása egy alkalmazáshoz a Azure Active Directory (Azure AD) bérlőben

Első lépések az egyszerűsített felhasználói bejelentkezések használatában az egyszeri bejelentkezés (SSO) beállításával az Azure Active Directory- (Azure AD-) bérlőhöz hozzáadott alkalmazásokhoz. Az SSO beállítása után a felhasználók azure AD hitelesítő adataik használatával bejelentkeznek az alkalmazásokba. Az SSO az Azure AD ingyenes kiadásában található meg.

További információ az OIDC-alapú egyszeri bejelentkezésről: [Understand OIDC-based single sign-on (Az OIDC-alapú egyszeri bejelentkezés megismerőe).](configure-oidc-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Az SSO beállításához az Azure AD-bérlőhöz hozzáadott alkalmazásokhoz a következő szükséges:

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Az alábbi szerepkörök egyike: globális rendszergazda, felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda vagy a szolgáltatásnév tulajdonosa.
- Egy alkalmazás, amely támogatja az SSO-t, és amely már előre konfigurálva van, és hozzá lett adva az Azure AD-katalógushoz. A legtöbb alkalmazás használhatja az Azure AD-t az SSO-hoz. Az Azure AD-katalógusban található alkalmazások előre konfigurálva vannak. Ha az alkalmazás nem szerepel a listán, vagy egyénileg fejlesztett alkalmazás, akkor is használhatja az Azure AD-val. Tekintse meg a tartalomjegyzékben található oktatóanyagokat és egyéb dokumentációt. Ez a rövid útmutató azokra az alkalmazásokra összpontosít, amelyek előre konfigurálva voltak az SSO-hoz, és az alkalmazásfejlesztők hozzáadták őket az Azure AD-katalógushoz.
- Nem kötelező: Az alkalmazások [megtekintésének befejezése.](view-applications-portal.md)
- Nem kötelező: Az [Alkalmazás hozzáadása elem befejezése.](add-application-portal.md)
- Nem kötelező: Az alkalmazás [konfigurálásának befejezése.](add-application-portal-configure.md)
- Nem kötelező: A felhasználók [alkalmazáshoz való hozzárendelésének befejezése.](add-application-portal-assign-users.md)

>[!IMPORTANT]
>A rövid útmutató lépéseit nem éles környezetben tesztelje.

## <a name="enable-single-sign-on-for-an-app"></a>Egyszeri bejelentkezés engedélyezése egy alkalmazáshoz

Ha olyan alkalmazást ad hozzá, amely az SSO-hez az OIDC szabványt használja, akkor egy beállítási gomb található. Amikor a gombra kattint, az alkalmazás webhelyére lép, és befejezi az alkalmazás regisztrációját. Az alkalmazások hozzáadásának folyamatát a sorozat korábbi, Alkalmazás hozzáadása gyorsútmutatója fedi le. Ha már hozzáadott alkalmazást konfigurál, nézze meg az első rövid útmutatót. Végigvezeti a bérlőn már telepített alkalmazások megtekintésén. 

Egyszeri bejelentkezés beállítása egy alkalmazáshoz:

1. A sorozat korábbi, rövid útmutatója azt is bemutatja, hogyan adhat hozzá olyan alkalmazást, amely az Azure AD-bérlőt fogja használni az identitáskezeléshez. Ha az alkalmazás fejlesztője az OIDC szabványt használta az SSO megvalósításához, akkor az alkalmazás hozzáadásakor egy regisztráció gombot fog látni. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="Képernyőkép az egyszeri bejelentkezés lehetőségről és a regisztráció gombról." lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. Válassza **a Regisztráció lehetőséget,** és meg fog jelenni az alkalmazásfejlesztők bejelentkezési oldala. Jelentkezzen be Azure Active Directory hitelesítő adataival. 

   > [!IMPORTANT]
    > Ha már rendelkezik előfizetéssel az alkalmazáshoz, a rendszer érvényesíti a felhasználói adatokat és a bérlői/címtáradatokat. Ha az alkalmazás nem tudja ellenőrizni a felhasználót, akkor átirányítja az alkalmazásszolgáltatásra való regisztrációhoz vagy a hibalapra.

3. A sikeres hitelesítés után megjelenik egy párbeszédpanel, amely rendszergazdai jóváhagyást kér. Válassza **a Jóváhagyás a szervezet nevében lehetőséget, majd** válassza az Elfogadás **lehetőséget.** A felhasználói és rendszergazdai hozzájárulással kapcsolatos további információkért lásd: A felhasználói és rendszergazdai [hozzájárulás megismerve.](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="Képernyőkép egy alkalmazás hozzájárulási képernyőről." lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. A rendszer hozzáadja az alkalmazást a bérlőhöz, és megjelenik az alkalmazás kezdőlapja.


> [!TIP]
> Az alkalmazáskezelést a következő Graph API automatizálhatja: [Alkalmazáskezelés automatizálása a Microsoft Graph API-val.](/graph/application-saml-sso-configure-api)

Lássunk egy videót, amely az OIDC-alapú alkalmazások Azure AD-hez való hozzáadásának további részleteit tartalmazza.

OIDC-alapú alkalmazás hozzáadása a Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett ezzel a rövid útmutató-sorozattal, érdemes törölnie az alkalmazást a tesztbérlő törléséhez. Az alkalmazás törlését a sorozat utolsó rövid útmutatója fedi le. Lásd: [Alkalmazás törlése.](delete-application-portal.md)

## <a name="next-steps"></a>Következő lépések

A következő cikk azt is bemutatja, hogyan törölhet alkalmazásokat.
> [!div class="nextstepaction"]
> [Alkalmazás törlése](delete-application-portal.md)
