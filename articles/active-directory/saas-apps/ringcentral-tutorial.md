---
title: 'Oktatóanyag: Azure Active Directory integráció a RingCentral-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és RingCentral között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 20e59f134c51662f9530862790d5a47b8bb21d6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650722"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Oktatóanyag: a RingCentral és a Azure Active Directory integrálása

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a RingCentral a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az RingCentral-t az Azure AD-vel, a következőket teheti:

* A RingCentral-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a RingCentral az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* RingCentral egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A RingCentral támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.

## <a name="add-ringcentral-from-the-gallery"></a>RingCentral hozzáadása a gyűjteményből

A RingCentral Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a RingCentral a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **RingCentral** kifejezést a keresőmezőbe.
1. Válassza ki a **RingCentral** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-ringcentral"></a>Azure AD SSO konfigurálása és tesztelése a RingCentral-hez

Konfigurálja és tesztelje az Azure AD SSO-t a RingCentral a **Britta Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a RingCentral-ben.

Az Azure AD SSO és a RingCentral konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[RINGCENTRAL SSO konfigurálása](#configure-ringcentral-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre RingCentral-teszt felhasználót](#create-ringcentral-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-RingCentral rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **RingCentral** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal** rendelkezik, hajtsa végre a következő lépéseket:

    1. Kattintson a **metaadat-fájl feltöltése** elemre.
    1. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés** elemre.
    1. A metaadat-fájl feltöltése után a rendszer az **alapszintű SAML-konfiguráció** szakaszban automatikusan feltölti az **azonosítót** és a **Válasz URL-** értékeket.

    > [!Note]
    > A **szolgáltatói metaadat-fájlt** a RingCentral SSO konfigurációs lapján szerezheti be, amelyet az oktatóanyag későbbi részében ismertetünk.

1. Ha nem rendelkezik **szolgáltatói metaadat-fájllal**, adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címek egyikét:
  
    | Azonosító |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címek egyikét:

    | Válasz URL-cím |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a RingCentral.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **RingCentral** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-ringcentral-sso"></a>RingCentral SSO konfigurálása

1. A RingCentral belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **RingCentral beállítása** elemre, majd a RingCentral alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a RingCentral való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-7-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a RingCentral, nyisson meg egy új böngészőablakot, és jelentkezzen be a RingCentral vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

1. A felső részen kattintson az **eszközök** elemre.

    ![A képernyőképen a RingCentral vállalati webhelyről kiválasztott eszközök láthatók.](./media/ringcentral-tutorial/ringcentral-1.png)

1. Navigáljon az **egyszeri bejelentkezéshez**.

    ![Képernyőfelvétel: a Tools (eszközök) menüből kiválasztva egy Sign-On.](./media/ringcentral-tutorial/ringcentral-2.png)

1. Az **egyszeri bejelentkezési** oldalon az **SSO-konfiguráció** szakaszban az **1. lépésben** kattintson a **Szerkesztés** elemre, és hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az S S O konfiguráció lapja, amelyen kiválaszthatja a szerkesztés lehetőséget.](./media/ringcentral-tutorial/ringcentral-3.png)

1. Az **egyszeri bejelentkezés beállítása** oldalon hajtsa végre a következő lépéseket:

    ![A képernyőképen az egy Sign-On beállítása oldalon látható, ahol feltöltheti a D P-metaadatokat.](./media/ringcentral-tutorial/ringcentral-4.png)

    a. Kattintson a **Tallózás** gombra a Azure Portal letöltött metaadat-fájl feltöltéséhez.

    b. A metaadatok feltöltése után az értékek automatikusan feltöltve lesznek az **SSO általános információi** szakaszban.

    c. Az **attribútum-hozzárendelés** szakaszban válassza **az e-mail attribútum** leképezése a következőre lehetőséget `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Kattintson a **Mentés** gombra.

    e. A **2. lépésben** kattintson a **Letöltés** gombra a **szolgáltatói metaadat-fájl** letöltéséhez, és töltse fel azt az **SAML alapszintű konfigurációs** szakaszba az **azonosító** és a **Válasz URL-cím** értékének automatikus kitöltéséhez Azure Portalban.

    ![Képernyőfelvétel: az S S O konfiguráció lapja, amelyen kiválaszthatja a letöltés lehetőséget.](./media/ringcentral-tutorial/ringcentral-6.png) 

    f. Ugyanazon a lapon navigáljon az **SSO engedélyezése** szakaszhoz, és hajtsa végre a következő lépéseket:

    ![A képernyőképen az Enable S S O szakasz látható, ahol befejezheti a konfigurációt.](./media/ringcentral-tutorial/ringcentral-5.png)

    * Válassza az **egyszeri bejelentkezési szolgáltatás engedélyezése** lehetőséget.

    * Jelölje be az **SSO vagy RingCentral hitelesítő adatokkal való bejelentkezés engedélyezése a felhasználóknak** jelölőnégyzetet.

    * Kattintson a **Mentés** gombra.

### <a name="create-ringcentral-test-user"></a>RingCentral-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a RingCentral-ben. A [RingCentral ügyfél-támogatási csapattal](https://success.ringcentral.com/RCContactSupp) együttműködve veheti fel a felhasználókat a RingCentral-platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a RingCentral, amelyhez be szeretné állítani az egyszeri bejelentkezést.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások RingCentral csempére kattint, automatikusan be kell jelentkeznie arra a RingCentral, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

A RingCentral konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).