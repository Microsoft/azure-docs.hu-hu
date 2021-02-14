---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Pipedrive | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Pipedrive között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 6f0002f3b8de76bded536c4f434f757c4ac96df6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382542"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pipedrive"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Pipedrive

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Pipedrive a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Pipedrive-t az Azure AD-vel, a következőket teheti:

* A Pipedrive-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Pipedrive az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Pipedrive egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Pipedrive támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO

## <a name="add-pipedrive-from-the-gallery"></a>Pipedrive hozzáadása a gyűjteményből

A Pipedrive Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Pipedrive a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Pipedrive** kifejezést a keresőmezőbe.
1. Válassza ki a **Pipedrive** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-pipedrive"></a>Azure AD SSO konfigurálása és tesztelése a Pipedrive-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Pipedrive a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Pipedrive-ben.

Az Azure AD SSO és a Pipedrive konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[PIPEDRIVE SSO konfigurálása](#configure-pipedrive-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre Pipedrive-teszt felhasználót](#create-pipedrive-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Pipedrive rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Pipedrive** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp/metadata.xml`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<COMPANY-NAME>.pipedrive.com/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a Pipedrive ügyfélszolgálati [csapatához](mailto:support@pipedrive.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Pipedrive alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a Pipedrive alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name | Forrás attribútum|
    | ------------ | --------- |
    | e-mail | User. mail |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse azt a számítógépre, és másolja az **alkalmazás-összevonási metaadatok URL-címét** , és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](./media/pipedrive-tutorial/certificate-data.png)

1. A **Pipedrive beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Pipedrive.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Pipedrive** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-pipedrive-sso"></a>Pipedrive SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Pipedrive webhelyre.

1. Kattintson a **felhasználói profil** elemre, és válassza a **Beállítások** lehetőséget.

    ![A "felhasználói profil" menüből kiválasztott "Settings" (beállítások) elemet megjelenítő képernyőkép.](./media/pipedrive-tutorial/configure-1.png)

1. Görgessen le a Security Center webhelyre, és válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Képernyőfelvétel: a "Security Center" kiválasztott "egyszeri bejelentkezés".](./media/pipedrive-tutorial/configure-2.png)

1. A **Pipedrive SAML-konfiguráció** szakaszának végrehajtásához hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely az összes Kiemelt szövegmezővel jeleníti meg a "S M L konfiguráció a Pipedrive" szakaszt.](./media/pipedrive-tutorial/configure-3.png)

    a. A **kiállító** szövegmezőbe illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portal másolt.

    b. Az **egyszeri bejelentkezés (SSO) URL-címe** szövegmezőbe illessze be a **bejelentkezési URL** -címet, amelyet a Azure Portal másolt.

    c. Az egyszeri Kijelentkezés **(SLO) URL-címe** szövegmezőbe illessze be a **kijelentkezési URL** értékét, amelyet a Azure Portal másolt.

    d. Az **x. 509 tanúsítvány** szövegmezőben nyissa meg a letöltött **tanúsítvány (Base64)** fájlt Azure Portal a Jegyzettömbben, és másolja ki a tartalmat, és illessze be az **x. 509 tanúsítvány** szövegmezőbe, és mentse a módosításokat.

### <a name="create-pipedrive-test-user"></a>Pipedrive-tesztelési felhasználó létrehozása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Pipedrive webhelyre.

1. Görgessen le a vállalathoz, és válassza a **felhasználók kezelése** lehetőséget.

    ![Képernyőfelvétel: a "vállalati" menüből kiválasztott "felhasználók kezelése".](./media/pipedrive-tutorial/user-1.png)

1. Kattintson a **felhasználók hozzáadása** elemre.
    
    ![A jobb oldalon kiválasztott "felhasználók hozzáadása" gombra kattintva megjelenő képernyőkép a "felhasználók kezelése" oldalon látható.](./media/pipedrive-tutorial/user-2.png)

1. A **felhasználók kezelése** szakaszban hajtsa végre a következő lépéseket:

    ![Pipedrive-konfiguráció](./media/pipedrive-tutorial/user-3.png)

    a. Az **e-mail** szövegmezőbe írja be a felhasználó e-mail címét, például: `B.Simon@contoso.com` .

    b. **A Utónév szövegmezőbe** írja be a felhasználó utónevét.

    c. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét.

    d. Kattintson **a megerősítés gombra, és hívja meg a felhasználókat**.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 


Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Pipedrive bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a Pipedrive bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Pipedrive, amelyhez be szeretné állítani az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások Pipedrive csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat kezdeményezéséhez, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra a Pipedrive, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

A Pipedrive konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
