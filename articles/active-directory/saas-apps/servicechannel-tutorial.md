---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a ServiceChannel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ServiceChannel között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 413ffa54a7413ad9b2482a3a8b6c698b34116301
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729836"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicechannel"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a ServiceChannel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ServiceChannel a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az ServiceChannel-t az Azure AD-vel, a következőket teheti:

* A ServiceChannel-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a ServiceChannel az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* ServiceChannel egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A ServiceChannel támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A ServiceChannel **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-servicechannel-from-the-gallery"></a>ServiceChannel hozzáadása a gyűjteményből

A ServiceChannel Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ServiceChannel a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **ServiceChannel** kifejezést a keresőmezőbe.
1. Válassza ki a **ServiceChannel** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-servicechannel"></a>Azure AD SSO konfigurálása és tesztelése a ServiceChannel-hez

Konfigurálja és tesztelje az Azure AD SSO-t a ServiceChannel a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a ServiceChannel-ben.

Az Azure AD SSO és a ServiceChannel konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SERVICECHANNEL SSO konfigurálása](#configure-servicechannel-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre ServiceChannel-teszt felhasználót](#create-servicechannel-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ServiceChannel rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **ServiceChannel** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az értéket a következőként: `http://adfs.<domain>.com/adfs/service/trust`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<customer domain>.servicechannel.com/saml/acs`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Itt javasoljuk, hogy a karakterlánc egyedi értékét használja az azonosítóban. Az értékek lekéréséhez forduljon a ServiceChannel ügyfélszolgálati [csapatához](https://servicechannel.zendesk.com/hc/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A szerepkör-jogcím előre konfigurálva van, így nem kell konfigurálnia, de az Azure AD-ben is létre kell hoznia őket a jelen [cikk](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)használatával. A jogcímekre vonatkozó további útmutatásért tekintse meg a [ServiceChannel útmutatót.](https://servicechannel.zendesk.com/hc/articles/217514326-Azure-AD-Configuration-Example)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **ServiceChannel beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a ServiceChannel.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **ServiceChannel** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a fentiekben ismertetett módon állította be a szerepköröket, kiválaszthatja a **szerepkör kiválasztása** legördülő listából.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-servicechannel-sso"></a>ServiceChannel SSO konfigurálása

Ha az egyszeri bejelentkezést szeretné konfigurálni a **ServiceChannel** oldalon, el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt url-címeket a Azure Portalról a [ServiceChannel támogatási csapatához](https://servicechannel.zendesk.com/hc/). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-servicechannel-test-user"></a>ServiceChannel-tesztelési felhasználó létrehozása

Az alkalmazás a felhasználó üzembe helyezését támogatja, és a hitelesítéssel rendelkező felhasználók automatikusan jönnek létre az alkalmazásban. A teljes jogú felhasználó kiépítés esetén forduljon a [ServiceChannel támogatási csapatához](https://servicechannel.zendesk.com/hc/).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a ServiceChannel, amelyhez be szeretné állítani az egyszeri bejelentkezést

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások ServiceChannel csempére kattint, automatikusan be kell jelentkeznie arra a ServiceChannel, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A ServiceChannel konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
