---
title: 'Oktatóanyag: Azure Active Directory integráció a Zoho One szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Zoho között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 225d6b13c882566a6b71c5809d67955a27561ed6
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99821058"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Oktatóanyag: Azure Active Directory integráció a Zoho One-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Zoho One-t Azure Active Directory (Azure AD) használatával. Ha az Azure AD-val integrálja a Zoho-t, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Zoho One-hoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Zoho-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt a Zoho-nal szeretné konfigurálni, a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Zoho egyszeri bejelentkezésre alkalmas előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Zoho One támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-zoho-one-from-the-gallery"></a>A Zoho hozzáadása a katalógusból

A Zoho egy Azure AD-integrációjának konfigurálásához hozzá kell adnia a Zoho One-t a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Zoho** kifejezést a keresőmezőbe.
1. Válassza ki a **Zoho egyet** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-zoho-one"></a>Azure AD SSO konfigurálása és tesztelése a Zoho One-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a Zoho használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és egy, a Zoho-beli kapcsolódó felhasználó között.

Ha az Azure AD SSO-t a Zoho használatával szeretné konfigurálni és tesztelni, hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az egyszeri bejelentkezés **[beállításainak konfigurálása az](#configure-zoho-one-sso)** alkalmazás oldalán.
    1. **[Hozzon létre egy felhasználói fiókhoz tartozó Zoho-felhasználót](#create-zoho-one-test-user)** –, hogy a B. Simon a Zoho egyik partnere, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Zoho One** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet: `one.zoho.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Az előző **Válasz URL-címének** értéke nem valódi. A következőt kell megadnia `<saml-identifier>` : #step4 a **Zoho egyszeri bejelentkezés** szakasza, amelyet az oktatóanyag későbbi részében ismertetünk.

    c. Kattintson a **további URL-címek beállítása** elemre.

    d. A **továbbítási állapot** szövegmezőbe írja be az URL-címet: `https://one.zoho.com`

5. Ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépést:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Az előző **bejelentkezési URL-cím** értéke nem valódi. A rendszer a tényleges Sign-On URL-címmel frissíti az értéket a következőt: a **Zoho egyszeri bejelentkezés** szakasza, amelyet az oktatóanyag későbbi részében ismertetünk. 

6. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **Zoho One szakasz beállítása** lapon másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban lehetővé teszi, hogy a B. Simon használja az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Zohohez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Zoho One** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-zoho-one-sso"></a>A Zoho egyszeri egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Zoho One vállalati webhelyre rendszergazdaként.

2. A **szervezet** lapon kattintson a **telepítés** SAML- **hitelesítés** alatt elemre.

    ![Zoho One org](./media/zoho-one-tutorial/set-up.png)

3. Az előugró oldalon hajtsa végre a következő lépéseket:

    ![Zoho One SIG](./media/zoho-one-tutorial/save.png)

    a. A **bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.

    b. A kijelentkezési **URL** szövegmezőbe illessze be a **KIJELENTKEZÉSI URL-cím** értékét, amelyet a Azure Portalból másolt.

    c. Kattintson a **Tallózás** gombra a Azure Portalról letöltött **tanúsítvány (Base64)** feltöltéséhez.

    d. Kattintson a **Mentés** gombra.

4. Miután mentette az SAML-hitelesítés telepítését, másolja az **SAML-azonosító** értéket, és fűzze hozzá a **Válasz URL-címéhez** a helyett `<saml-identifier>` , `https://accounts.zoho.com/samlresponse/one.zoho.com` és illessze be a generált értéket a **Válasz URL-címe** szövegmezőbe az **alapszintű SAML-konfiguráció** szakaszban.

    ![Zoho egy SAML](./media/zoho-one-tutorial/saml-identifier.png)

5. Lépjen a **tartományok** lapra, majd kattintson a **tartomány hozzáadása** lehetőségre.

    ![Zoho egy tartomány](./media/zoho-one-tutorial/add-domain.png)

6. A **tartomány hozzáadása** oldalon hajtsa végre a következő lépéseket:

    ![Zoho egy tartomány hozzáadása](./media/zoho-one-tutorial/add-domain-name.png)

    a. A **tartománynév** szövegmezőbe írja be a következőt: tartomány, például contoso.com.

    b. Kattintson a **Hozzáadás** parancsra.

    >[!Note]
    >A tartomány hozzáadása után kövesse az [alábbi](https://www.zoho.com/one/help/admin-guide/domain-verification.html) lépéseket a tartomány ellenőrzéséhez. A tartomány ellenőrzése után használja a tartománynevet a **bejelentkezési URL-cím** mezőben a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszában.

### <a name="create-zoho-one-test-user"></a>Zoho egy tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Zoho One-ba, a Zoho One-ba kell kiépíteni őket. A Zoho One-ban a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Zoho-ba egy biztonsági rendszergazdaként.

2. A **felhasználók** lapon kattintson a **felhasználói embléma** elemre.

    ![Zoho egy felhasználó](./media/zoho-one-tutorial/user.png)

3. A **felhasználó hozzáadása** oldalon hajtsa végre a következő lépéseket:

    ![Zoho One felhasználó hozzáadása](./media/zoho-one-tutorial/add-user.png)
    
    a. A **név** szövegmezőbe írja be a felhasználó nevét, például a **Britta Simon** nevet.
    
    b. Az **e-mail-cím** szövegmezőbe írja be a felhasználóhoz hasonló e-mail-címet brittasimon@contoso.com .

    >[!Note]
    >Válassza ki az ellenőrzött tartományt a tartomány listából.

    c. Kattintson a **Hozzáadás** parancsra.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a Zoho egy bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen a Zoho One bejelentkezési URL-címére közvetlenül, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Zoho-ra, amelyhez be szeretné állítani az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a My apps szolgáltatásban a Zoho One csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a Zoho-ba, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

A Zoho konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
