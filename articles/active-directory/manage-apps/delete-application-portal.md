---
title: 'Gyors útmutató: alkalmazás törlése a Azure Active Directory (Azure AD) bérlőből'
description: Ez a rövid útmutató a Azure Portal használatával törli az alkalmazást a Azure Active Directory (Azure AD) bérlőből.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 1/5/2021
ms.author: kenwith
ms.openlocfilehash: 59d6044cf42109501941b3f4ff54375aed7fab7c
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504872"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Gyors útmutató: alkalmazás törlése a Azure Active Directory (Azure AD) bérlőből

Ez a rövid útmutató a Azure Portal használatával törli az Azure Active Directory (Azure AD) bérlőhöz hozzáadott alkalmazást.

További információ az egyszeri bejelentkezésről és az Azure-ról: [Mi az az egyetlen Sign-On (SSO)](what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Ha törölni szeretne egy alkalmazást az Azure AD-bérlőből, a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A következő szerepkörök egyike: globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás rendszergazdája vagy az egyszerű szolgáltatásnév tulajdonosa.
- Nem kötelező: az [alkalmazások megtekintésének](view-applications-portal.md)befejezése.
- Nem kötelező: az [alkalmazás hozzáadásának](add-application-portal.md)befejezése.
- Nem kötelező: [alkalmazás konfigurálásának](add-application-portal-configure.md)befejezése.
- Nem kötelező: a [felhasználók hozzárendelésének befejezése egy alkalmazáshoz](add-application-portal-assign-users.md).
- Nem kötelező: az [egyszeri bejelentkezés beállításának](add-application-portal-setup-sso.md)befejezése.

>[!IMPORTANT]
>A rövid útmutató lépéseinek teszteléséhez használjon nem éles környezetet.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Alkalmazás törlése az Azure AD-bérlőből

Alkalmazás törlése az Azure AD-bérlőből:

1. Az Azure AD-portálon válassza a **vállalati alkalmazások** lehetőséget. Ezután keresse meg és válassza ki a törölni kívánt alkalmazást. Ebben az esetben törölte az előző rövid útmutatóban hozzáadott **GitHub_test** alkalmazást.
1. A bal oldali ablaktábla **kezelés** szakaszában válassza a **Tulajdonságok** lehetőséget.
1. Válassza a **Törlés** lehetőséget, majd kattintson az **Igen** gombra annak megerősítéséhez, hogy törölni szeretné az alkalmazást az Azure ad-bérlőből.

> [!TIP]
> Az Graph API segítségével automatizálhatja az alkalmazások felügyeletét: az [alkalmazások kezelésének automatizálása Microsoft Graph API-val](/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezzel a rövid útmutatóval elkészült, érdemes törölni az alkalmazást a tesztelési bérlő tisztításához. Az alkalmazás törlése ebben a rövid útmutatóban szerepelt.

## <a name="next-steps"></a>Következő lépések

Elvégezte a gyors üzembe helyezési sorozatot! Következő lépésként Ismerkedjen meg az egyszeri Sign-Onsal (SSO), lásd: [Mi az SSO?](what-is-single-sign-on.md) Vagy olvassa el az App Management ajánlott eljárásait.
> [!div class="nextstepaction"]
> [Alkalmazások kezelése – ajánlott eljárások](application-management-fundamentals.md)
