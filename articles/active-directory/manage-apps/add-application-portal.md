---
title: 'Gyors útmutató: alkalmazás hozzáadása a Azure Active Directory (Azure AD) bérlőhöz'
description: Ebben a rövid útmutatóban egy katalógusbeli alkalmazást adhat hozzá az Azure Portalon az Azure Active Directory- (Azure AD-) bérlőhöz.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: 2d3b0be88a23bbbb0573b4d11a2e5a39dc6254da
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259067"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Gyors útmutató: alkalmazás hozzáadása a Azure Active Directory (Azure AD) bérlőhöz

Az Azure Active Directory (Azure AD) rendelkezik egy katalógussal, amely több ezer előre integrált alkalmazást tartalmaz. A szervezet által használt alkalmazások többsége valószínűleg már szerepel a katalógusban.

Miután hozzáadta az alkalmazást az Azure AD-bérlőhöz, a következőket teheti:

- Konfigurálja az alkalmazás tulajdonságait.
- Az alkalmazás felhasználói hozzáférésének kezelése feltételes hozzáférési házirenddel.
- Konfigurálja az egyszeri bejelentkezést, hogy a felhasználók Azure AD-beli hitelesítő adataikkal be tudják jelentkezni az alkalmazásba.

## <a name="prerequisites"></a>Előfeltételek

Ha alkalmazást szeretne hozzáadni az Azure AD-bérlőhöz, a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A következő szerepkörök egyike: globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás rendszergazdája vagy az egyszerű szolgáltatásnév tulajdonosa.
- (Nem kötelező: [az alkalmazások megtekintésének](view-applications-portal.md)befejezése).

>[!IMPORTANT]
>Az ebben a rövid útmutatóban ismertetett lépések teszteléséhez nem éles környezet használatát javasoljuk.

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Alkalmazás hozzáadása az Azure AD-bérlőhöz

Alkalmazás hozzáadása az Azure AD-bérlőhöz:

1. A [Azure Portal](https://portal.azure.com)a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.
2. A **Azure Active Directory** ablaktáblán válassza a **vállalati alkalmazások** lehetőséget. Megnyílik a **minden alkalmazás** panel, és az Azure ad-bérlőben található alkalmazások véletlenszerű mintája jelenik meg.
3. A **vállalati alkalmazások** ablaktáblán válassza az **új alkalmazás** lehetőséget. 
    ![Válassza az új alkalmazás lehetőséget, ha egy katalógus-alkalmazást szeretne hozzáadni a bérlőhöz](media/add-application-portal/new-application.png)
4. Váltson az új katalógus előzetes verziójára: az **alkalmazás hozzáadása lap** tetején található szalagcímen válassza ki azt a hivatkozást, amely az **új és továbbfejlesztett alkalmazás-katalógus kipróbálásához kattintson ide**.
5. Megnyílik a **Tallózás Azure ad-katalógus (előzetes verzió)** panel, amelyen a Felhőbeli platformok, a helyszíni alkalmazások és a Kiemelt alkalmazások csempéi láthatók. A **Kiemelt alkalmazások** szakaszban felsorolt alkalmazások rendelkeznek ikonokkal, amelyek azt jelzik, hogy támogatják-e az összevont egyszeri bejelentkezést (SSO) és a létesítést. 
    ![Alkalmazás keresése név vagy kategória szerint](media/add-application-portal/browse-gallery.png)
6. Böngészhet a katalógusban a hozzáadni kívánt alkalmazáshoz, vagy megkeresheti az alkalmazást úgy, hogy beírja a nevét a keresőmezőbe. Ezután válassza ki az alkalmazást az eredmények közül. 
7. A következő lépés attól függ, hogy a fejlesztő milyen módon implementálta az alkalmazás egyszeri bejelentkezését (SSO). Az egyszeri bejelentkezés négyféle módon valósítható meg az alkalmazások fejlesztői számára. A négy módszer az SAML, az OpenID Connect, a Password és a linked. Alkalmazás hozzáadásakor kiválaszthatja, hogy csak a képernyőképen látható egyszeri bejelentkezést használó alkalmazások legyenek kiszűrve és megtekintve. Például az egyszeri bejelentkezés megvalósítására szolgáló népszerű standard neve Security Assertion Markup Language (SAML). Egy másik népszerű szabvány neve OpenId Connect (OIDC). Ha ezekkel a szabványokkal konfigurálja az egyszeri bejelentkezést, akkor jegyezze fel a hozzáadott alkalmazás által megvalósított egyszeri bejelentkezés típusát.

    :::image type="content" source="media/add-application-portal/sso-types.png" alt-text="A képernyőképen az SSO-típusok választója látható." lightbox="media/add-application-portal/sso-types.png":::

    - Ha az alkalmazás fejlesztője a **OIDC standardot** használta az egyszeri bejelentkezéshez, válassza a **regisztráció** lehetőséget. Megjelenik egy telepítési oldal. Ezután nyissa meg a rövid útmutatót a OIDC-alapú egyszeri bejelentkezés beállításához.
    :::image type="content" source="media/add-application-portal/sign-up-oidc-sso.png" alt-text="A képernyőképen egy OIDC-alapú SSO-alkalmazás hozzáadása látható.":::

    - Ha az alkalmazás fejlesztője az SAML- **szabványt** használta az egyszeri bejelentkezéshez, válassza a **Létrehozás** lehetőséget. Megjelenik az első lépések lap, amelyen a beállításokkal konfigurálhatja az alkalmazást a vállalat számára. Az űrlapon szerkesztheti az alkalmazás nevét, hogy megfeleljen a szervezet igényeinek. Ezután nyissa meg a rövid útmutatót az SAML-alapú egyszeri bejelentkezés beállításához.
    :::image type="content" source="media/add-application-portal/create-application.png" alt-text="A képernyőfelvételen egy SAML-alapú SSO-alkalmazás hozzáadása látható.":::


> [!IMPORTANT]
> Az SAML-alapú és a OIDC-alapú SSO-implementációk között fontos különbségek vannak. Az SAML-alapú alkalmazásokkal több példányt is hozzáadhat ugyanahhoz az alkalmazáshoz. Például: GitHub1, GitHub2 stb. OIDC-alapú alkalmazások esetében csak az alkalmazás egy példányát veheti fel. Ha már hozzáadott egy OIDC-alapú alkalmazást, és újra hozzá kívánja adni ugyanazt az alkalmazást, és kétszer is megadhatja azt, akkor a bérlő nem adja hozzá újra.

Ha a keresett alkalmazás nem szerepel a katalógusban, akkor kiválaszthatja a **saját alkalmazás létrehozása** lehetőséget, majd az **alkalmazással kapcsolatos teendőket?** válassza a katalógusban **nem található egyéb alkalmazások integrálását**. A Microsoft már dolgozott számos alkalmazás-fejlesztővel, hogy előre konfigurálja őket az Azure AD-vel való együttműködéshez. Az előre konfigurált alkalmazások megjelennek a katalógusban. Ha azonban a hozzáadni kívánt alkalmazás nem szerepel a listáján, létrehozhat egy új, általános, alkalmazást, és konfigurálhatja saját maga, vagy az azt létrehozó fejlesztő útmutatásával.

Elkészült egy alkalmazás hozzáadásával. A következő rövid útmutató bemutatja, hogyan módosíthatja az emblémát, és szerkesztheti az alkalmazás egyéb tulajdonságait.

> [!TIP]
> Az Graph API segítségével automatizálhatja az alkalmazások felügyeletét: az [alkalmazások kezelésének automatizálása Microsoft Graph API-val](/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a gyors üzembe helyezési sorozatot, akkor érdemes lehet törölni az alkalmazást a tesztelési bérlő törléséhez. Az alkalmazás törlését a sorozat utolsó rövid útmutatója tartalmazza. [alkalmazás törlése](delete-application-portal.md).

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan konfigurálhat egy alkalmazást.
> [!div class="nextstepaction"]
> [Alkalmazás konfigurálása](add-application-portal-configure.md)