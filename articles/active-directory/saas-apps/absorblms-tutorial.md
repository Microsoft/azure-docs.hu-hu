---
title: 'Oktatóanyag: Azure Active Directory integráció az LMS-sel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az LMS befogadására.
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
ms.openlocfilehash: 268943463002ddd1dbdbf67df9587758f81f537f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646520"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Oktatóanyag: Azure Active Directory integráció az LMS-sel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a befogadási LMS-t Azure Active Directory (Azure AD) használatával. Ha integrálja az LMS-t az Azure AD-vel, a következőket teheti:

* Vezérlő az Azure AD-ben, aki hozzáfér az LMS befogadásához.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az LMS és az Azure AD-fiókjuk befogadására.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a befogadási LMS szolgáltatással való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az LMS egyszeri bejelentkezést engedélyező előfizetésének befogadása.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A befogadási LMS támogatja a **identitásszolgáltató** KEZDEMÉNYEZett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-absorb-lms-from-the-gallery"></a>Elnyelő LMS hozzáadása a katalógusból

A befogadási LMS Azure AD-be való integrálásának konfigurálásához fel kell vennie az LMS szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **befogadja az LMS** elemet a keresőmezőbe.
1. Jelölje be az **LMS befogadása** az eredmények paneljén lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-absorb-lms"></a>Azure AD SSO konfigurálása és tesztelése a befogadási LMS számára

Konfigurálja és tesztelje az Azure AD SSO-t a befogadási LMS használatával egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az LMS-ben.

Az Azure AD SSO és a befogadási LMS használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[befogadási LMS egyszeri bejelentkezés konfigurálása](#configure-absorb-lms-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy befogadási LMS-teszt felhasználót](#create-absorb-lms-test-user)** –, hogy a B. Simon-nak egy, a felhasználó Azure ad-képviseletéhez kapcsolódó LMS-t tartalmazó.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **LMS** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** gombra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    Ha **5 felhasználói felületet** használ, használja a következő konfigurációt:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    Ha **5 – új tanulási élményt** használ, használja a következő konfigurációt:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek beszerzéséhez vegye fel a kapcsolatot az [LMS ügyfél-támogatási csapatával](https://support.absorblms.com/hc/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName** leképezéssel van leképezve.

    ![image](common/edit-attribute.png)

6. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **befogadási LMS beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az LMS befogadására való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **LMS befogadása** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-absorb-lms-sso"></a>A befogadási LMS egyszeri bejelentkezésének konfigurálása

1. Egy új böngészőablakban jelentkezzen be rendszergazdaként az LMS céges webhelyre.

2. Kattintson a jobb felső sarokban található **fiók** gombra.

    ![A fiók gomb](./media/absorblms-tutorial/account.png)

3. A fiók ablaktáblán válassza a **portál beállításai** lehetőséget.

    ![A portál beállításainak hivatkozása](./media/absorblms-tutorial/portal.png)

4. Válassza az **SSO-beállítások kezelése** lapot.

    ![A felhasználók lap](./media/absorblms-tutorial/sso.png)

5. Az **önálló Sign-On-beállítások kezelése** lapon tegye a következőket:

    ![Az egyszeri bejelentkezés konfigurációs lapja](./media/absorblms-tutorial/settings.png)

    a. A **név** szövegmezőbe írja be a nevet, például az Azure ad Marketplace egyszeri bejelentkezést.

    b. Válassza ki az **SAML** -t **metódusként**.

    c. Nyissa meg a Jegyzettömbben a Azure Portal letöltött tanúsítványt. Távolítsa el a **---BEGIN certificate---** és **---END Certificate---** címkéket. Ezt követően illessze be a többi tartalmat a **kulcs** mezőbe.

    d. A **Mode (mód** ) mezőben válassza az **identitás-szolgáltató kezdeményezve** lehetőséget.

    e. Az **azonosító tulajdonság** mezőben válassza ki azt az attribútumot, amelyet felhasználói azonosítóként konfigurált az Azure ad-ben. Ha például az *NameIdentifier* van kiválasztva az Azure ad-ben, válassza a **username (Felhasználónév**) lehetőséget.

    f. Válassza  ki a Sha256 **aláírási típusként**.

    : A **bejelentkezési URL-cím** mezőben illessze be a **felhasználói hozzáférési URL-címet** az Azure Portal alkalmazás **Tulajdonságok** lapjáról.

    h. A **kijelentkezési URL-címben** illessze be a Azure Portal a **bejelentkezés beállítása** ablakból másolt kijelentkezési **URL** -értéket.

    i. **Automatikus átirányításának** bekapcsolása **a** következőre:.

6. Válassza a **Mentés lehetőséget.**

    ![Az egyszeri bejelentkezési bejelentkezés engedélyezése](./media/absorblms-tutorial/save.png)

### <a name="create-absorb-lms-test-user"></a>Befogadási LMS-teszt felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek az LMS-be, be kell őket állítani az LMS-ben. A befogadási LMS esetében a kiépítés manuális feladat.

**A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a befogadása az LMS vállalati webhelyre rendszergazdaként.

2. A **felhasználók** ablaktáblán válassza a **felhasználók** lehetőséget.

    ![A felhasználók hivatkozás](./media/absorblms-tutorial/users.png)

3. Válassza a **felhasználó** fület.

    ![Az új legördülő lista hozzáadása](./media/absorblms-tutorial/add.png)

4. A **felhasználó hozzáadása** lapon tegye a következőket:

    ![A felhasználó hozzáadása lap](./media/absorblms-tutorial/user.png)

    a. Az **Utónév** mezőbe írja be az utónév nevet (például **Britta**).

    b. A **vezetéknév** mezőbe írja be a vezetéknevet, például **Simon** nevet.

    c. A **Felhasználónév** mezőbe írja be a teljes nevet, például **Britta Simon**.

    d. A **jelszó** mezőbe írja be a felhasználói jelszó mezőt.

    e. A **Jelszó megerősítése** mezőbe írja be újra a jelszót.

    f. **Az aktív váltógomb beállítása** **aktívra**.

5. Válassza a **Mentés lehetőséget.**

    ![Az egyszeri bejelentkezési bejelentkezés engedélyezése](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Alapértelmezés szerint a felhasználó kiépítés nincs engedélyezve az SSO-ban. Ha az ügyfél szeretné engedélyezni ezt a szolgáltatást, azt a [jelen](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) dokumentációban említetteknek megfelelően be kell állítani. Azt is vegye figyelembe, hogy a felhasználói elővision csak az **5 – új tanulói élmény** az ACS URL-címmel érhető el`https://company.myabsorb.com/api/rest/v2/authentication/saml`

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie a befogadási LMS-be, amelyhez be kell állítania az egyszeri bejelentkezést.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások alkalmazásban az LMS-csempére kattint, automatikusan be kell jelentkeznie a befogadási LMS-be, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A befogadási LMS konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).