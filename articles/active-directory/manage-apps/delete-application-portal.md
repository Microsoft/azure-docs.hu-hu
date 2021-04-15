---
title: 'Rövid útmutató: Alkalmazás törlése a Azure Active Directory (Azure AD)-bérlőből'
description: Ez a rövid útmutató Azure Portal használatával töröl egy alkalmazást a Azure Active Directory (Azure AD) bérlőjébe.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 1/5/2021
ms.author: iangithinji
ms.openlocfilehash: 55607d520affe704cecd2e16fb64b3a65c04e167
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374301"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Rövid útmutató: Alkalmazás törlése a Azure Active Directory (Azure AD)-bérlőből

Ez a rövid útmutató Azure Portal használatával töröl egy alkalmazást, amely hozzá lett adva a Azure Active Directory (Azure AD) bérlőjéhez.

További információ az SSO-val és az Azure-ral kapcsolatban: Mi az [Sign-On (SSO)](what-is-single-sign-on.md)?

## <a name="prerequisites"></a>Előfeltételek

Ha törölni szeretne egy alkalmazást az Azure AD-bérlőből, a következőre lesz szüksége:

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- A következő szerepkörök egyike: globális rendszergazda, felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda vagy a szolgáltatásnév tulajdonosa.
- Nem kötelező: Az alkalmazások [megtekintése befejeztével.](view-applications-portal.md)
- Nem kötelező: Az [Alkalmazás hozzáadása elem befejezése.](add-application-portal.md)
- Nem kötelező: Az alkalmazás [konfigurálása befejeztével.](add-application-portal-configure.md)
- Nem kötelező: A felhasználók [alkalmazáshoz való hozzárendelésének befejezése.](add-application-portal-assign-users.md)
- Nem kötelező: Az [Egyszeri bejelentkezés beállítása befejezése.](add-application-portal-setup-sso.md)

>[!IMPORTANT]
>A rövid útmutató lépéseit nem éles környezetben tesztelje.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Alkalmazás törlése az Azure AD-bérlőből

Alkalmazás törlése az Azure AD-bérlőből:

1. Az Azure AD portálon válassza a Vállalati **alkalmazások lehetőséget.** Ezután keresse meg és válassza ki a törölni kívánt alkalmazást. Ebben az esetben töröltünk egy GitHub_test **előző** rövid útmutatóban hozzáadott alkalmazásból.
1. A bal **oldali panel Kezelés** szakaszában válassza a Tulajdonságok **lehetőséget.**
1. Válassza **a Törlés** lehetőséget, majd válassza az **Igen** lehetőséget annak megerősítéséhez, hogy törölni szeretné az alkalmazást az Azure AD-bérlőből.

> [!TIP]
> Az alkalmazáskezelést a következő Graph API automatizálhatja: [Alkalmazáskezelés automatizálása a Microsoft Graph API-val.](/graph/application-saml-sso-configure-api)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett ezzel a rövid útmutató-sorozattal, érdemes törölnie az alkalmazást a tesztbérlő törléséhez. Az alkalmazás törlésével ez a rövid útmutatóban volt érintett.

## <a name="next-steps"></a>Következő lépések

Befejezte a gyorsútmutató-sorozatot! Ezután ismerje meg az egyszeri bejelentkezést (SSOSign-On) a [Mi az az SSO?](what-is-single-sign-on.md) Vagy olvassa el az alkalmazáskezelés ajánlott eljárásait.
> [!div class="nextstepaction"]
> [Ajánlott alkalmazáskezelési eljárások](application-management-fundamentals.md)
