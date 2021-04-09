---
title: 'Oktatóanyag: Azure Active Directory az integráció a Zohorel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Zoho között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: c5778f39a5091753a1658ec121379a4ed29a7542
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648373"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Oktatóanyag: Azure Active Directory integráció a Zoho-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Zohot Azure Active Directory (Azure AD) használatával. A Zoho Azure AD-val való integrálásával a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Zohohoz.
* Lehetővé teheti a felhasználók számára, hogy az Azure AD-fiókjával automatikusan bejelentkezzenek a Zohoba.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt a Zoho-nal szeretné konfigurálni, a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Zoho egyszeri bejelentkezésre engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Zoho támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="add-zoho-from-the-gallery"></a>Zoho hozzáadása a katalógusból

A Zoho Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Zohoot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Zoho** kifejezést a keresőmezőbe.
1. Válassza ki a **Zoho** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-zoho"></a>Azure AD SSO konfigurálása és tesztelése a Zoho-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a Zoho használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a Zoho kapcsolódó felhasználója között.

Az Azure AD SSO és a Zoho konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Zoho SSO konfigurálása](#configure-zoho-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy Zoho test User](#create-zoho-test-user)** -t, hogy rendelkezzen a "B. Simon in Zoho" jogosultsággal, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Zoho** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<company name>.zohomail.com`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a [Zoho ügyfél-támogatási csapatával](https://www.zoho.com/mail/contact.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Zoho beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD-tesztkörnyezet létrehozása 

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a Zoho elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Zoho** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a &quot;default Access&quot; szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name=&quot;configure-zoho-sso&quot;></a>A Zoho SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Zoho levelezési céges webhelyre rendszergazdaként.

2. Nyissa meg a **Vezérlőpultot**.
   
    ![Vezérlőpult](./media/zoho-mail-tutorial/control-panel.png &quot;Vezérlőpult")

3. Kattintson az **SAML-hitelesítés** fülre.
   
    ![SAML-hitelesítés](./media/zoho-mail-tutorial/saml-authentication.png "SAML-hitelesítés")

4. Az **SAML-hitelesítés részletei** szakaszban hajtsa végre a következő lépéseket:
   
    ![SAML-hitelesítés részletei](./media/zoho-mail-tutorial/details.png "SAML-hitelesítés részletei")
   
    a. A **bejelentkezési URL** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-címet** .
   
    b. A **kijelentkezési URL** szövegmezőbe illessze be az Azure Portalból másolt **KIJELENTKEZÉSI URL-címet** .
   
    c. A **jelszó módosítása URL-cím** szövegmezőbe illessze be a **jelszó módosítása URL-címet** , amelyet a Azure Portal másolt.
       
    d. Nyissa meg Azure Portal a Jegyzettömbből letöltött Base-64 kódolású tanúsítványt, másolja a tartalmát a vágólapra, majd illessze be a **PublicKey** szövegmezőbe.
   
    e. **Algoritmusként** válassza az **RSA** lehetőséget.
   
    f. Kattintson az **OK** gombra.

### <a name="create-zoho-test-user"></a>Zoho-teszt felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Zoho mailbe, a Zoho-mailbe kell azokat kiépíteni. A Zoho-levelezés esetén a kiépítés manuális feladat.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a Zoho levelezési felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a **Zoho levelezési** céges webhelyre rendszergazdaként.

1. Nyissa meg a **Vezérlőpult \> levelezési & dokumentumait**.

1. Lépjen a **felhasználói adatok felhasználó \> hozzáadása** elemre.
   
    ![Képernyőfelvétel: a Zoho levelezési webhely a felhasználó adataival és a kiválasztott felhasználó hozzáadásával jelenik meg.](./media/zoho-mail-tutorial/add-user-1.png "Felhasználó hozzáadása")

1. A **felhasználók hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Képernyőfelvétel: a felhasználók hozzáadása párbeszédpanel, ahol megadhatja a leírt értékeket.](./media/zoho-mail-tutorial/add-user-2.png "Felhasználó hozzáadása")
   
    a. Az **Utónév** szövegmezőbe írja be a felhasználó utónevét, például a **Britta** nevet.

    b. A **vezetékneve** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon** nevet.

    c. Az **e-mail-azonosító** szövegmezőbe írja be a felhasználó e-mail azonosítóját, például **brittasimon \@ contoso.com**.

    d. A **jelszó** szövegmezőbe írja be a felhasználó jelszavát.
   
    e. Kattintson az **OK** gombra.  
      
    > [!NOTE]
    > A Azure Active Directory fiók tulajdonosa egy e-mailt fog kapni, amely egy hivatkozással megerősíti a fiókot, mielőtt az aktívvá válna.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ekkor a rendszer átirányítja a Zoho bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Zoho bejelentkezési URL-címére, és indítsa el innen a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások Zoho csempére kattint, automatikusan be kell jelentkeznie a Zoho-ba, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A Zoho konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).