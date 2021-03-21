---
title: 'Oktatóanyag: Azure Active Directory az üvegházhatású integrációval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az üvegház között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/25/2020
ms.author: jeedes
ms.openlocfilehash: 77f72d6c63231f0854b58470f86c65ffc81c9775
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98731920"
---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Oktatóanyag: Azure Active Directory üvegházhatású integrációval

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az üvegházhatást Azure Active Directory (Azure AD) használatával. Az üvegház az Azure AD-vel való integrálásakor a következőket teheti:

* Az Üvegházhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az üvegházba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az üvegházhatást okozó egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

> [!NOTE] 
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben. 

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az üvegház támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-greenhouse-from-the-gallery"></a>Üvegház hozzáadása a katalógusból

Az üvegház Azure AD-integrációjának konfigurálásához az üvegházat hozzá kell adnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **üvegházhatás** kifejezést a keresőmezőbe.
1. Válassza az **üvegház** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-greenhouse"></a>Az Azure AD SSO konfigurálása és tesztelése az üvegházban

Konfigurálja és tesztelje az Azure AD SSO-t az üvegház használatával egy **B. Simon** nevű teszt felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az üvegházban.

Az Azure AD SSO az Üvegházral történő konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. Az **[üvegházhatású egyszeri bejelentkezés konfigurálása](#configure-greenhouse-sso)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy üvegházhatást okozó tesztet használó felhasználót](#create-greenhouse-test-user)** – hogy rendelkezzen egy olyan Britta, amely a felhasználó Azure ad-képviseletéhez kapcsolódik.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **üvegházhatású** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.greenhouse.io`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.greenhouse.io`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az alábbi értékek beszerzéséhez vegye fel a kapcsolatot az [üvegházhatású ügyfelek ügyfélszolgálatával](https://www.greenhouse.io/contact) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. Az **üvegház beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az Üvegházhoz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **üvegház** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-greenhouse-sso"></a>Üvegházhatású SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az üvegházhatású webhelyre rendszergazdaként.

1. Lépjen a **Configure > fejlesztői központ > egyszeri bejelentkezés elemre**.

    ![az SSO-oldal képernyőképe](./media/greenhouse-tutorial/configure.png)

1. Hajtsa végre a következő lépéseket az egyetlen Sign-On oldalon.

    ![az SSO konfigurációs oldalának képernyőképe](./media/greenhouse-tutorial/sso-page.png)

    a. Másolja az SSO-azonosító **fogyasztói URL-címét** értékre, illessze be ezt az értéket a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszában található **Válasz URL** szövegmezőbe.

    b. Az **entitás azonosítója/kiállító** szövegmezőbe illessze be azt az **Azure ad-azonosító** értéket, amelyet a Azure Portal másolt.

    c. Az **egyszeres Sign-On URL** szövegmezőbe illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portalból másolt.

    d. Nyissa meg a letöltött **összevonási metaadatokat tartalmazó XML-fájlt** a Azure Portal a Jegyzettömbben, és illessze be a tartalmat a **identitásszolgáltató Certificate ujjlenyomat** szövegmezőbe.

    e. Válassza ki a **név azonosítójának formátuma** értéket a legördülő listából.

    f. Kattintson a **tesztelés megkezdése** elemre.

    >[!NOTE]
    >Azt is megteheti, hogy az **összevonási metaadatok XML-** fájlját is feltölti a **fájl kiválasztása** lehetőségre kattintva.

### <a name="create-greenhouse-test-user"></a>Üvegházhatású teszt felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek az üvegházba, az üvegházban kell kiépíteni őket. Üvegházhatás esetén a kiépítés manuális feladat.

>[!NOTE]
>Az üvegház által biztosított bármely más, az üvegházhatást okozó felhasználói fiók létrehozására szolgáló eszközt vagy API-t az Azure AD felhasználói fiókjainak kiépítéséhez használhatja. 

**Felhasználói fiókok kiépítéséhez hajtsa végre a következő lépéseket:**

1. Jelentkezzen be az **üvegházhatást okozó** céges webhelyre rendszergazdaként.

2. Ugrás a **> felhasználók konfigurálása új felhasználók >**
   
    ![Felhasználók](./media/greenhouse-tutorial/create-user-1.png "Felhasználók")

4. Az **új felhasználók hozzáadása** szakaszban hajtsa végre a következő lépéseket:
   
    ![Új felhasználó hozzáadása](./media/greenhouse-tutorial/create-user-2.png "Új felhasználó hozzáadása")

    a. A **felhasználói e-mailek megadása** szövegmezőbe írja be a kiépíteni kívánt érvényes Azure Active Directory fiók e-mail-címét.

    b. Kattintson a **Mentés** gombra.    
   
      >[!NOTE]
      >A Azure Active Directory fiók tulajdonosai e-mailt kapnak, beleértve a fiók megerősítését, mielőtt az aktív állapotba kerül.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja az üvegházhatást okozó bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül az üvegházhatású bejelentkezési URL-címre, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások üvegház csempére kattint, a rendszer átirányítja az üvegházhatást okozó bejelentkezési URL-címre. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

Az üvegház konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).