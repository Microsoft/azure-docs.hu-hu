---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az HRworks Single Sign-Ontal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és HRworks egyszeri bejelentkezés között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: d6b23213d9d986c62a227b3e182a22896d128222
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92442541"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az HRworks egyetlen Sign-On

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az HRworks egyetlen Sign-Ont Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja az HRworks egyetlen Sign-Onét, a következőket teheti:

* A HRworks egyszeri bejelentkezéshez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek, hogy egyetlen Sign-On HRworks az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* HRworks egyszeri Sign-On egyszeri bejelentkezéses (SSO) előfizetés engedélyezett.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az HRworks Single Sign-On támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>HRworks egyetlen Sign-On hozzáadása a katalógusból

Ahhoz, hogy az HRworks egyetlen Sign-On integrálását az Azure AD-be konfigurálja, hozzá kell adnia egy HRworks-Sign-On a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **HRworks egyszeri bejelentkezés** elemet a keresőmezőbe.
1. Válassza az **HRworks egyszeri bejelentkezés** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése egyetlen Sign-On HRworks

Az Azure AD SSO konfigurálása és tesztelése egyetlen Sign-On HRworks egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az egyszeri bejelentkezés HRworks.

Az Azure AD SSO HRworks egyszeri bejelentkezéssel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[HRworks egyszeri Sign-On egyszeri bejelentkezés konfigurálása](#configure-hrworks-single-sign-on-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre egy HRworks-Sign-On tesztelési felhasználóval](#create-hrworks-single-sign-on-test-user)** , hogy a B. Simon partnere legyen a HRworks egyetlen Sign-On, amely a felhasználó Azure ad-képviseletéhez van csatolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **HRworks egyszeri bejelentkezési** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez vegye fel a kapcsolatot az [HRworks egyetlen Sign-On ügyfél-támogatási csapatával](mailto:nadja.sommerfeld@hrworks.de) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **HRworks egyszeri bejelentkezés beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a HRworks egyszeri bejelentkezéshez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **HRworks egyszeri bejelentkezés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-hrworks-single-sign-on-sso"></a>HRworks egyszeri Sign-On egyszeri bejelentkezés konfigurálása

1. Ha az HRworks egyszeri bejelentkezésen belül szeretné automatizálni a konfigurációt, telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **HRworks egyszeri bejelentkezés beállítása** lehetőségre a HRworks Single Sign-On alkalmazásra. Itt adja meg a rendszergazdai hitelesítő adatokat az HRworks egyszeri bejelentkezésre való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-4-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani az HRworks-Sign-On, nyisson meg egy új böngészőablakot, és jelentkezzen be a HRworks egyetlen Sign-On vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

1. Kattintson a **rendszergazda**  >  **alapjai**  >  **biztonsági**  >  **egyszeri bejelentkezés** lehetőségre a menüsáv bal oldalán, és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Jelölje be az **egyszeri bejelentkezés használata** jelölőnégyzetet.

    b. Adja meg az **XML-metaadatokat** **meta adatbeviteli módszerként**.

    c. Válassza az **Egyéni NameID azonosító** **értékeként a NameID értéket**.

    d. Nyissa meg a Jegyzettömbben a Azure Portal letöltött metaadatok XML-fájlját, másolja a tartalmát, majd illessze be a **metaadatok** szövegmezőbe.

    e. Kattintson a **Mentés** gombra.

### <a name="create-hrworks-single-sign-on-test-user"></a>HRworks egyszeri Sign-On tesztelési felhasználó létrehozása

Az Azure AD-felhasználók engedélyezéséhez jelentkezzen be az HRworks egyszeri bejelentkezésbe, az HRworks egyszeri bejelentkezéssel kell kiépíteni őket. HRworks egyszeri bejelentkezés esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be az HRworks egyetlen Sign-On rendszergazdaként.

1. A menüsáv bal oldalán kattintson a **rendszergazda**  >  **személyek**  >  **személyre**  >  **új személy** elemre.

     ![Képernyőfelvétel: a H R Works oldala, melyen személyeket és új személyeket jelölt ki.](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. Az előugró ablakban kattintson a **tovább** gombra.

    ![A képernyőképen a személy számára kiválasztható országok listája látható.](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. Az **új személy létrehozása országba jogi feltételek** előugró ablakban adja meg a megfelelő adatokat, például az **Utónév** és a **vezetéknév nevet** , majd kattintson a **Létrehozás** gombra.

    ![Képernyőfelvétel: szövegmezők, ahol megadhatja a személy első és utolsó nevét.](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a HRworks Single Sign-On csempére kattint, automatikusan be kell jelentkeznie a HRworks egyetlen Sign-On, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Az HRworks egyetlen Sign-On kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)