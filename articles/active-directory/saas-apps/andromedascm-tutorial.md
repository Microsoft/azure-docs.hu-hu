---
title: 'Oktatóanyag: Azure Active Directory integráció az Andromeda szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az Andromeda között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 678bb46efb19f3451566306ddbbe372c631c717e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736038"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Oktatóanyag: Azure Active Directory integráció az Andromeda-vel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az Andromeda-t Azure Active Directory (Azure AD) használatával.
Az Andromeda az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá az Andromeda-hez.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Andromeda (egyszeri bejelentkezés) alkalmazásba az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az Andromeda-sel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Andromeda egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az Andromeda támogatja **az SP-t és a identitásszolgáltató** KEZDEMÉNYEZett SSO
* Az Andromeda **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-andromeda-from-the-gallery"></a>Az Andromeda hozzáadása a gyűjteményből

Az Andromeda Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Androméda-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **Androméda** kifejezést.
1. Válassza az **Androméda** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-andromeda"></a>Azure AD SSO konfigurálása és tesztelése az Andromeda-hez

Konfigurálja és tesztelje az Azure AD SSO-t az Andromeda használatával egy **B. Simon** nevű teszt felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és az Andromeda-beli kapcsolódó felhasználó között.

Az Azure AD SSO az Andromeda-vel való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:


1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. Az **[Andromeda SSO konfigurálása](#configure-andromeda-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. Az **[Andromeda-teszt felhasználójának létrehozása](#create-andromeda-test-user)** – a Britta Simon in Andromeda partnere, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **Androméda** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenantURL>.ngcxpress.com/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Képernyőfelvétel: további U R ls beállítása, ahol megadhatja a bejelentkezést az U R L-ben.](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Az értéket a tényleges azonosító, a válasz URL-cím és a Sign-On URL-cím alapján kell frissíteni, amelyet később az oktatóanyag ismertet.

6. Az Androméda-alkalmazás meghatározott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon, a **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![A képernyőképen a felhasználói attribútumok (például givenName user. givenName és EmailAddress user. mail) láthatók.](common/edit-attribute.png)

    > [!Important]
    > A névtér-definíciók törlése a beállítás beállításakor.

7. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában szerkessze a jogcímeket a **Szerkesztés ikon** használatával, vagy adja hozzá a jogcímeket az **új jogcím hozzáadása** paranccsal az SAML-token attribútum konfigurálásához a fenti képen látható módon, és hajtsa végre a következő lépéseket: 

    | Name | Forrás attribútum|
    | ------ | -----------|
    | szerepkör        | Alkalmazás-specifikus szerepkör |
    | típus        | Alkalmazás típusa |
    | cég       | CompanyName |

    > [!NOTE]
    > Az Andromeda szerepköröket vár az alkalmazáshoz hozzárendelt felhasználók számára. Állítsa be ezeket a szerepköröket az Azure AD-ben, hogy a felhasználók hozzá lehessen rendelni a megfelelő szerepköröket. A szerepkörök Azure AD-ben való konfigurálásának megismeréséhez lásd [itt](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![Képernyőfelvétel: a felhasználói jogcímek új jogcím hozzáadására és mentésre vonatkozó lehetőségekkel.](common/new-save-attribute.png)

    ![Képernyőfelvétel: a felhasználói jogcímek kezelése, ahol megadhatja az ebben a lépésben ismertetett értékeket.](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként** lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson **az OK** gombra

    : Kattintson a **Mentés** gombra.

8. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

9. Az **Andromeda beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az Andromeda elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **Andromeda** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a fentiekben ismertetett módon állította be a szerepköröket, kiválaszthatja a **szerepkör kiválasztása** legördülő listából.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-andromeda-sso"></a>Az Andromeda SSO konfigurálása

1. Jelentkezzen be az Andromeda vállalati webhelyre rendszergazdaként.

2. A menüsáv tetején kattintson a **rendszergazda** lehetőségre, és navigáljon a **felügyelet** elemre.

    ![Androméda-rendszergazda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. Kattintson a kezelőfelület bal oldalán az **illesztőfelületek** szakaszban az SAML- **konfiguráció** elemre.

    ![Andromeda SAML](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Az **SAML-konfiguráció** szakasz lapon hajtsa végre a következő lépéseket:

    ![Andromeda-konfiguráció](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Jelölje be **az egyszeri bejelentkezés engedélyezése az SAML-vel** lehetőséget.

    b. Az **Andromeda-információ** szakasz alatt másolja **az SP Identity** értéket, és illessze be az **alapszintű SAML-konfigurációs** szakasz **azonosító** szövegmezőbe.

    c. Másolja a **fogyasztói URL-címet** , és illessze be az **alapszintű SAML konfigurációs** szakasz **Válasz URL-** szövegmezőbe.

    d. Másolja a **bejelentkezési URL-címet** , és illessze be az **alapszintű SAML konfigurációs** szakasz **bejelentkezési URL** szövegmezőbe.

    e. Az **SAML-identitás szolgáltatója** szakaszban adja meg a identitásszolgáltató nevét.

    f. Az **egyszeri bejelentkezés végpontjának** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portal másolt.

    : Nyissa meg a letöltött **Base64-kódolású tanúsítványt** Azure Portal a Jegyzettömbben, illessze be az **X 509 tanúsítvány** szövegmezőbe.
    
    h. Rendelje hozzá a következő attribútumokat a megfelelő értékkel, hogy elősegítse az egyszeri bejelentkezést az Azure AD-ben. A bejelentkezéshez a **User ID** attribútum szükséges. A kiépítés, az **e-mailek**, a **vállalat**, a **UserType** és a **szerepkör** megadása kötelező. Ebben a szakaszban az attribútumok leképezését (név és értékek) határozzák meg, amelyek korrelálnak a Azure Portal

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Kattintson a **Mentés** gombra.


### <a name="create-andromeda-test-user"></a>Andromeda-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre az Andromeda-ben. Az Andromeda az igény szerinti felhasználói üzembe helyezést támogatja, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik az Andromeda-ben, a rendszer egy újat hoz létre a hitelesítés után. Ha manuálisan kell létrehoznia egy felhasználót, forduljon az [Andromeda ügyfél-támogatási csapathoz](https://www.ngcsoftware.com/support/).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ekkor a rendszer átirányítja az Androméda bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül az Andromeda bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra az Andromeda-re, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások alkalmazásban az Andromeda csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra az Andromeda-ba, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

Az Androméda konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
