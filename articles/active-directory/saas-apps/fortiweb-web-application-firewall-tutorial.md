---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a FortiWeb webalkalmazási tűzfallal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és FortiWeb webalkalmazási tűzfal között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: e34664bd81023da7a50b8ff4645c670146ef2554
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731936"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortiweb-web-application-firewall"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a FortiWeb webalkalmazási tűzfallal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az FortiWeb webalkalmazási tűzfalat Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az FortiWeb webalkalmazási tűzfalát az Azure AD-vel, a következőket teheti:

* A FortiWeb webalkalmazási tűzfalhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a webalkalmazási tűzfalba az Azure AD-fiókokkal való FortiWeb.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* FortiWeb webalkalmazási tűzfal egyszeri bejelentkezéses (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A FortiWeb webalkalmazási tűzfal támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

## <a name="adding-fortiweb-web-application-firewall-from-the-gallery"></a>FortiWeb webalkalmazási tűzfal hozzáadása a gyűjteményből

A FortiWeb webalkalmazási tűzfal Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a FortiWeb webalkalmazási tűzfalat a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **FortiWeb webalkalmazási tűzfal** kifejezést a keresőmezőbe.
1. Válassza az **FortiWeb webalkalmazási tűzfal** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-fortiweb-web-application-firewall"></a>Az Azure AD SSO konfigurálása és tesztelése a FortiWeb webalkalmazási tűzfalon

Konfigurálja és tesztelje az Azure AD SSO-t a FortiWeb webalkalmazási tűzfallal egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a FortiWeb webalkalmazási tűzfalon.

Az Azure AD SSO az FortiWeb webalkalmazási tűzfallal való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[FortiWeb webalkalmazási tűzfal egyszeri bejelentkezésének konfigurálása](#configure-fortiweb-web-application-firewall-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[FortiWeb-webalkalmazási tűzfal tesztelése – felhasználó létrehozása](#create-fortiweb-web-application-firewall-test-user)** – a FortiWeb webalkalmazási tűzfalban található, a felhasználó Azure ad-képviseletéhez kapcsolódó webalkalmazási tűzfalon.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **FortiWeb webalkalmazás-tűzfal** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

   a. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.<CUSTOMER_DOMAIN>.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.<CUSTOMER_DOMAIN>.com/<FORTIWEB_NAME>/saml.sso/SAML2/POST`

    c. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.<CUSTOMER_DOMAIN>.com`

    d. A **kijelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.<CUSTOMER_DOMAIN>.info/<FORTIWEB_NAME>/saml.sso/SLO/POST`
 
    > [!NOTE]
    > `<FORTIWEB_NAME>` egy olyan név-azonosító, amelyet később a konfiguráció FortiWeb való megadásakor fog használni.
    > A valós URL-értékek eléréséhez forduljon a [FortiWeb webalkalmazási tűzfal támogatási csapatához](mailto:support@fortinet.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)


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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a FortiWeb webalkalmazási tűzfalhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **FortiWeb webalkalmazási tűzfal** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-fortiweb-web-application-firewall-sso"></a>A FortiWeb webalkalmazási tűzfal egyszeri bejelentkezésének konfigurálása

1.  Navigáljon `https://<address>:8443` a `<address>` FortiWeb virtuális géphez rendelt teljes tartománynév vagy nyilvános IP-cím helyére.

2.  Jelentkezzen be a FortiWeb virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.

1. Hajtsa végre a következő lépéseket a következő lapon.

    ![SAML-kiszolgáló lapja](./media/fortiweb-web-application-firewall-tutorial/configure-sso.png)

    a.  A bal oldali menüben kattintson a **felhasználó** elemre.

    b.  A felhasználó területen kattintson a **Távoli kiszolgáló** elemre.

    c.  Kattintson az **SAML-kiszolgáló** elemre.

    d.  Kattintson az **új létrehozása** gombra.

    e.  A **név** mezőben adja meg az `<fwName>` Azure ad konfigurálása szakaszban használt értéket.

    f.  Az **entitás-azonosító** szövegmezőbe illessze be azt az **Azure ad-azonosító** értéket, amelyet a Azure Portal másolt.

    : A **metaadatok** mellett kattintson a **fájl kiválasztása** elemre, és válassza ki az **összevonási metaadatok XML-** fájlját, amelyet a Azure Portal letöltött.

    h.  Kattintson az **OK** gombra.

### <a name="create-a-site-publishing-rule"></a>Hely közzétételi szabályának létrehozása

1.  Navigáljon `https://<address>:8443` a `<address>` FortiWeb virtuális géphez rendelt teljes tartománynév vagy nyilvános IP-cím helyére.

1.  Jelentkezzen be a FortiWeb virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.
1. Hajtsa végre a következő lépéseket a következő lapon.

    ![Hely közzétételi szabálya](./media/fortiweb-web-application-firewall-tutorial/site-publish-rule.png)

    a.  A bal oldali menüben kattintson az **alkalmazás kézbesítése** lehetőségre.
    
    b.  Az **alkalmazás kézbesítése** területen kattintson a **hely közzététele** lehetőségre.
    
    c.  A **hely közzététele** területen kattintson a **hely közzététele** lehetőségre.
    
    d.  Kattintson a **webhely közzétételi szabálya** elemre.
    
    e.  Kattintson az **új létrehozása** gombra.
    
    f.  Adja meg a hely közzétételi szabályának nevét.
    
    :  A **közzétett hely típusa** mellett kattintson a **reguláris kifejezés** elemre.
    
    i.  A **közzétett hely** mellett adjon meg egy karakterláncot, amely megfelel a közzétenni kívánt webhely állomásfejléc-nek.
    
    j.  Az **elérési út** mellett adja meg a/elemet.
    
    k.  Az **ügyfél-hitelesítési módszer** mellett válassza az **SAML-hitelesítés** lehetőséget.
    
    l.  Az **SAML-kiszolgáló** legördülő menüben válassza ki a korábban létrehozott SAML-kiszolgálót.
    
    m.  Kattintson az **OK** gombra.


### <a name="create-a-site-publishing-policy"></a>Hely közzétételi szabályzatának létrehozása

1.  Navigáljon `https://<address>:8443` a `<address>` FortiWeb virtuális géphez rendelt teljes tartománynév vagy nyilvános IP-cím helyére.

2.  Jelentkezzen be a FortiWeb virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.

1. Hajtsa végre a következő lépéseket a következő lapon.

    ![Webhely-közzétételi házirend](./media/fortiweb-web-application-firewall-tutorial/site-publish-policy.png)

    a.  A bal oldali menüben kattintson az **alkalmazás kézbesítése** lehetőségre.

    b.  Az **alkalmazás kézbesítése** területen kattintson a **hely közzététele** lehetőségre.

    c.  A **hely közzététele** területen kattintson a **hely közzététele** lehetőségre.

    d.  Kattintson a **hely közzététele házirend** elemre.

    e.  Kattintson az **új létrehozása** gombra.

    f.  Adja meg a hely közzétételi házirendjének nevét.

    :  Kattintson az **OK** gombra.

    h.  Kattintson az **új létrehozása** gombra.

    i.  A **szabály** legördülő menüben válassza ki a korábban létrehozott webhely-közzétételi szabályt.

    j.  Kattintson az **OK** gombra.

### <a name="create-and-assign-a-web-protection-profile"></a>Webvédelmi profil létrehozása és társítása

1.  Navigáljon `https://<address>:8443` a `<address>` FortiWeb virtuális géphez rendelt teljes tartománynév vagy nyilvános IP-cím helyére.

2.  Jelentkezzen be a FortiWeb virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.
3.  A bal oldali menüben kattintson a **házirend** elemre.
4.  A **házirend** területen kattintson a **webvédelmi profil** elemre.
5.  Kattintson a **beágyazott standard védelem** elemre, majd a **klónozás** elemre.
6.  Adja meg az új webes védelmi profil nevét, majd kattintson **az OK** gombra.
7.  Válassza ki az új webes védelmi profilt, és kattintson a **Szerkesztés** gombra.
8.  A **Webhely közzététele** mellett válassza ki a korábban létrehozott webhely-közzétételi szabályzatot.
9.  Kattintson az **OK** gombra.
 
    ![Webhely közzététele](./media/fortiweb-web-application-firewall-tutorial/web-protection.png)

10. A bal oldali menüben kattintson a **házirend** elemre.
11. A **házirend** területen kattintson a **kiszolgáló házirend** elemre.
12. Válassza ki a webhely közzétételéhez használt kiszolgálói házirendet, amelyhez Azure Active Directory szeretne használni a hitelesítéshez.
13. Kattintson a **Szerkesztés** gombra.
14. A **web Protection-profil** legördülő menüben válassza ki az imént létrehozott webvédelmi profilt.
15. Kattintson az **OK** gombra.
16. Próbálja meg elérni azt a külső URL-címet, amelyhez a FortiWeb közzéteszi a webhelyet. A hitelesítéshez át kell irányítani Azure Active Directory.


### <a name="create-fortiweb-web-application-firewall-test-user"></a>FortiWeb webalkalmazási tűzfal tesztelése felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a FortiWeb webalkalmazási tűzfalon. A [FortiWeb webalkalmazási tűzfal támogatási csapatának](mailto:support@fortinet.com) használata a felhasználók hozzáadásához a FortiWeb webalkalmazási tűzfal platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a FortiWeb webalkalmazás bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Nyissa meg közvetlenül a FortiWeb webalkalmazás-bejelentkezési URL-címét, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások FortiWeb webalkalmazás csempére kattint, a rendszer átirányítja a FortiWeb webalkalmazás bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A FortiWeb webalkalmazási tűzfal konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).