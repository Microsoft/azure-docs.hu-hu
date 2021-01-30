---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Sugar-CRM-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Sugar-CRM között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 9cd91bf40354a40129d20a6ed0381801fece4ba4
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063138"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Sugar-CRM-sel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Sugar-CRM-t Azure Active Directory (Azure AD) használatával. Ha a Sugar-CRM-t az Azure AD-vel integrálja, a következőket teheti:

* Szabályozza az Azure AD-t, hogy ki férhet hozzá a Sugar CRM-hez.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Sugar CRM-be az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A cukor CRM egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Sugar CRM támogatja az **SP** által KEZDEMÉNYEZett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-sugar-crm-from-the-gallery"></a>Sugar CRM hozzáadása a katalógusból

A Sugar-CRM Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Sugar CRM-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Sugar CRM** kifejezést a keresőmezőbe.
1. Válassza ki a **SUGAR CRM** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-sugar-crm"></a>Azure AD SSO konfigurálása és tesztelése a Sugar CRM-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Sugar CRM használatával egy **B. Simon** nevű tesztelési felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Sugar-CRM-ben.

Az Azure AD SSO és a Sugar-CRM konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[cukor CRM SSO konfigurálása](#configure-sugar-crm-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy cukor CRM-teszt felhasználót](#create-sugar-crm-test-user)** – hogy rendelkezzen a B. Simon a cukor CRM-ben lévő, a felhasználó Azure ad-beli képviseletéhez kapcsolódó partnerével.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **SUGAR CRM** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:

    - `https://<companyname>.sugarondemand.com`
    - `https://<companyname>.trial.sugarcrm`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

    - `https://<companyname>.sugarondemand.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.eu/<companyname>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel és a válasz URL-címével. Az értékek beszerzéséhez vegye fel a kapcsolatot a [SUGAR CRM-ügyfél támogatási csoportjával](https://support.sugarcrm.com/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Sugar-CRM beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Sugar CRM-hez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **SUGAR CRM** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sugar-crm-sso"></a>A Sugar-CRM SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Sugar CRM-beli vállalati webhelyre rendszergazdaként.

1. Nyissa meg a **rendszergazdát**.

    ![Felügyelet](./media/sugarcrm-tutorial/ic795888.png "Rendszergazda")

1. Az **Adminisztráció** szakaszban kattintson a **jelszó-kezelés** elemre.

    ![Képernyőfelvétel: az adminisztráció szakasz, ahol kiválaszthatja a jelszavas kezelés lehetőséget.](./media/sugarcrm-tutorial/ic795889.png "Felügyelet")

1. Válassza az **SAML-hitelesítés engedélyezése** lehetőséget.

    ![A képernyőfelvételen az SAML-hitelesítés kiválasztására szolgáló lehetőség látható.](./media/sugarcrm-tutorial/ic795890.png "Felügyelet")

1. Az **SAML hitelesítés** szakaszban hajtsa végre a következő lépéseket:

    ![SAML-hitelesítés](./media/sugarcrm-tutorial/save.png "SAML-hitelesítés")  

    a. A **bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.
  
    b. A **slo URL-címe** szövegmezőbe illessze be a **KIJELENTKEZÉSI URL-címet**, amelyet a Azure Portalból másolt.
  
    c. Nyissa meg a Base-64 kódolású tanúsítványt a Jegyzettömbben, másolja vágólapra a tartalmát, majd illessze be a teljes tanúsítványt **X. 509 tanúsítvány** szövegmezőbe.
  
    d. Kattintson a **Mentés** gombra.

### <a name="create-sugar-crm-test-user"></a>Sugar CRM-beli tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Sugar CRM-be, ki kell építeni őket a Sugar CRM-be. A cukor CRM esetében a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **SUGAR CRM** -beli vállalati webhelyre rendszergazdaként.

1. Nyissa meg a **rendszergazdát**.

    ![Felügyelet](./media/sugarcrm-tutorial/ic795888.png "Rendszergazda")

1. Az **Adminisztráció** szakaszban kattintson a **felhasználói kezelés** elemre.

    ![Képernyőfelvétel: az adminisztráció szakasz, ahol kiválaszthatja a felhasználók kezelése lehetőséget.](./media/sugarcrm-tutorial/ic795893.png "Felügyelet")

1. Lépjen a **felhasználók \> új felhasználó létrehozása** gombra.

    ![Új felhasználó létrehozása](./media/sugarcrm-tutorial/ic795894.png "Új felhasználó létrehozása")

1. A **felhasználói profil** lapon hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a felhasználói profil lap, ahol megadhatja a leírt értékeket.](./media/sugarcrm-tutorial/ic795895.png "Új felhasználó")

    * Írja be egy érvényes Azure Active Directory felhasználó **felhasználónevét**, **vezetéknevét** és **e-mail-címét** a kapcsolódó szövegmezőbe.
  
1. **Állapotként** válassza az **aktív** lehetőséget.

1. A Password (jelszó) lapon hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a Password (jelszó) lap, amelyen megadhatja a leírt értékeket.](./media/sugarcrm-tutorial/ic795896.png "Új felhasználó")

    a. Írja be a jelszót a kapcsolódó szövegmezőbe.

    b. Kattintson a **Mentés** gombra.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a cukor CRM által biztosított felhasználói fiók létrehozására szolgáló eszközt vagy API-t is használhat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a Sugar-CRM bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Sugar CRM bejelentkezési URL-címére, és indítsa el innen a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Sugar CRM-csempére kattint, a rendszer átirányítja a Sugar-CRM-bejelentkezési URL-címre. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

A Sugar-CRM konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
