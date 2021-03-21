---
title: 'Oktatóanyag: Azure Active Directory integráció a MobileIron-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és MobileIron között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: c47092b1488a79805db69308bcb9a8efde1c0d58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653038"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Oktatóanyag: Azure Active Directory integráció a MobileIron

 Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a MobileIron a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az MobileIron-t az Azure AD-vel, a következőket teheti:

* A MobileIron-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a MobileIron az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* MobileIron egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A MobileIron **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.

## <a name="add-mobileiron-from-the-gallery"></a>MobileIron hozzáadása a gyűjteményből

A MobileIron Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a MobileIron a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **MobileIron** kifejezést a keresőmezőbe.
1. Válassza ki a **MobileIron** az eredmények közül, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-mobileiron"></a>Azure AD SSO konfigurálása és tesztelése a MobileIron-hez

Konfigurálja és tesztelje az Azure AD SSO-t a MobileIron-mel egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a MobileIron-ben.

Az Azure AD SSO és a MobileIron konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
     1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. **[MOBILEIRON SSO konfigurálása](#configure-mobileiron-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre MobileIron-teszt felhasználót](#create-mobileiron-test-user)** – hogy a MobileIron Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD SSO-t a Azure Portalban.
 
1. A Azure Portal **MobileIron** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyetlen Sign-On módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.MobileIron.com/<key>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<host>.MobileIron.com/saml/SSO/alias/<key>`

    c. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

     A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<host>.MobileIron.com/user/login.html`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és Sign-On URL-címmel. A kulcs és a gazdagép értékét a MobileIron felügyeleti portálján érheti el, amelyet később az oktatóanyagban talál.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszót.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezi a B. Simon számára az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a MobileIron.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **MobileIron** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **Felhasználó hozzáadása** elemet. Ezután a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza ki a felhasználók listájából a **B. Simon** elemet. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-mobileiron-sso"></a>MobileIron SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a MobileIron vállalati webhelyre rendszergazdaként.

2. Nyissa meg a **rendszergazdai**  >  **identitást** , és válassza a **HRE** lehetőséget a **Felhőbeli identitásszolgáltató beállítás** mezőjében.

    ![A képernyőképen a MobileIron webhely adminisztrátor lapja látható, ahol a személyazonosság van kiválasztva.](./media/MobileIron-tutorial/tutorial_MobileIron_admin.png)

3. Másolja ki a **kulcs** és a **gazdagép** értékeit, és illessze be őket az **alapszintű SAML-konfigurációs** szakasz url-címeinek befejezéséhez Azure Portal.

    ![A képernyőfelvételen látható az SAML beállítás beállítása egy kulcs és egy gazdagép értékkel.](./media/MobileIron-tutorial/key.png)

4. A **metaadatok exportálása a HRE és az Importálás a MobileIron felhőbe mezőben** kattintson a **fájl kiválasztása** lehetőségre a letöltött metaadatoknak a Azure Portalból való feltöltéséhez. Kattintson a **kész** gombra a feltöltés után.

    ![Egyetlen Sign-On rendszergazdai metaadatok konfigurálása gomb](./media/MobileIron-tutorial/tutorial_MobileIron_adminmetadata.png)


### <a name="create-mobileiron-test-user"></a>MobileIron-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a MobileIron, a MobileIron kell kiépíteni őket.  
MobileIron esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a MobileIron vállalati webhelyre rendszergazdaként.

1. Lépjen a **felhasználók** elemre, és kattintson az  >  **egyetlen felhasználó** hozzáadása lehetőségre.

    ![Egyetlen Sign-On felhasználó beállítása gomb](./media/MobileIron-tutorial/tutorial_MobileIron_user.png)

1. Az **"egyetlen felhasználó"** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyetlen Sign-On felhasználó hozzáadása gomb konfigurálása](./media/MobileIron-tutorial/tutorial_MobileIron_useradd.png)

    a. Az **E-mail cím** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet brittasimon@contoso.com .

    b. Az **Utónév** szövegmezőbe írja be a felhasználó utónevét, például a Britta nevet.

    c. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például Simon nevet.

    d. Kattintson a **Kész** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a MobileIron bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a MobileIron bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a MobileIron, amelyhez be szeretné állítani az egyszeri bejelentkezést.

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások MobileIron csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat kezdeményezéséhez, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra a MobileIron, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A MobileIron konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáféréstől. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
