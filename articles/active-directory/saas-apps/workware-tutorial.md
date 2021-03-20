---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Workware | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Workware között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: f4996943fd95252be45f7a270d143f14e7f2d48a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181271"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workware"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Workware

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Workware a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Workware-t az Azure AD-vel, a következőket teheti:

* A Workware-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Workware az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Workware egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Workware támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-workware-from-the-gallery"></a>Workware hozzáadása a gyűjteményből

A Workware Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Workware a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Workware** kifejezést a keresőmezőbe.
1. Válassza ki a **Workware** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-workware"></a>Azure AD SSO konfigurálása és tesztelése a Workware-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Workware a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Workware-ben.

Az Azure AD SSO és a Workware konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[WORKWARE SSO konfigurálása](#configure-workware-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre Workware-teszt felhasználót](#create-workware-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Workware rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Workware** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `<WORKWARE_URL>/WW/AuthServices`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `<WORKWARE_URL>/WW/AuthServices/Acs`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek lekéréséhez forduljon a Workware ügyfélszolgálati [csapatához](mailto:support@activeops.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Workware beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Workware.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Workware** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-workware-sso"></a>Workware SSO konfigurálása

A Workware SSO funkciójának használatához a következő telepítést kell végrehajtani:

#### <a name="enable-sso-permissions-for-workware-system-administrators"></a>Egyszeri bejelentkezési engedélyek engedélyezése a Workware rendszergazdák számára

* Ahhoz, hogy a rendszergazdák Workware az egyszeri bejelentkezéses hitelesítést, engedélyezni kell az egyszeri bejelentkezéses hitelesítés engedélyét (a **Rendszerbeállítások > jogosultságok a szerepkör képernyőhöz > rendszerkonfigurációs engedélyek kategóriájában** ) az Workware-rendszergazdák számára.

    ![Egyszeri bejelentkezéses hitelesítés engedélyezése](./media/workware-tutorial/permission.png)

#### <a name="set-up-sso-authentication-in-workware"></a>Egyszeri bejelentkezéses hitelesítés beállítása a Workware-ben

1. Lépjen a **Rendszerbeállítások** lapra, és kattintson az **egyszeri bejelentkezéses hitelesítés** elemre.

1. Az **egyszeri bejelentkezéses hitelesítés** szakaszban kattintson az **egyszeri bejelentkezéses hitelesítés hozzáadása** gombra, és hajtsa végre a következő lépéseket: 

    ![Egyszeri bejelentkezéses hitelesítés](./media/workware-tutorial/authentication.png)

    1. A **külső identitás szolgáltatójában** adja meg a identitásszolgáltató nevét.
    1. **SAML 2.0** kiválasztása **hitelesítési típusként**
    1. Az **Identity Provider bejelentkezési URL** szövegmezőben adja meg a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.
    1. Az **Identity Provider kiállító URL-címe** szövegmezőben adja meg az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt.
    1. Az **Identitáskezelő kijelentkezési URL-címe** szövegmezőbe írja be a **kijelentkezési URL** értékét, amelyet a Azure Portal másolt.
    1. Kattintson az **Engedélyezés** gombra.
    1. Töltse fel a letöltött **tanúsítványt** az **identitás-szolgáltatói tanúsítványba** a Azure Portalból.
    1. Kattintson a **Mentés** gombra.


### <a name="create-workware-test-user"></a>Workware-tesztelési felhasználó létrehozása

1. Jelentkezzen be rendszergazdaként a Workware webhelyre.

1. Válassza a **rendszergazda > > felhasználói fiókok létrehozása/megtekintése > új hozzáadása**

1. Hajtsa végre a következő lépéseket a következő lapon.

    ![Felhasználó tesztelése](./media/workware-tutorial/create-user.png)

    a. Adjon meg egy érvényes nevet a **név** mezőben.

    b. Válassza a **hitelesítési típust** **SSO**-ként.

    c. Adja meg a kötelező mezőket, majd kattintson a **Save (Mentés**) gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Workware, amelyhez be szeretné állítani az egyszeri bejelentkezést

* Használhatja a Microsoft Access panelt. Ha a hozzáférési panelen a Workware csempére kattint, automatikusan be kell jelentkeznie arra a Workware, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Következő lépések

A Workware konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).