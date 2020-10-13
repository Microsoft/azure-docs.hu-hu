---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a CloudPassage | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és CloudPassage között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: 8fdb59db7e11e3c8b946be940a2f98bd42810b88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91758353"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a CloudPassage

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a CloudPassage a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az CloudPassage-t az Azure AD-vel, a következőket teheti:

* A CloudPassage-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a CloudPassage az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* CloudPassage egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A CloudPassage támogatja az **SP** által KEZDEMÉNYEZett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-cloudpassage-from-the-gallery"></a>CloudPassage hozzáadása a gyűjteményből

A CloudPassage Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a CloudPassage a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **CloudPassage** kifejezést a keresőmezőbe.
1. Válassza ki a **CloudPassage** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a CloudPassage

Konfigurálja és tesztelje az Azure AD SSO-t a CloudPassage a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a CloudPassage-ben.

Az Azure AD SSO és a CloudPassage konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[CLOUDPASSAGE SSO konfigurálása](#configure-cloudpassage-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre CloudPassage-teszt felhasználót](#create-cloudpassage-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-CloudPassage rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **CloudPassage** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

     a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://portal.cloudpassage.com/saml/init/accountid`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://portal.cloudpassage.com/saml/consume/accountid` . Az attribútum értékét az CloudPassage-portál **egyszeri bejelentkezés beállításai** szakaszának egyszeri bejelentkezési beállítások szakaszában található **SSO beállítási dokumentáció** elemre kattintva érheti el.

    ![Képernyőfelvétel: a CloudPassage-portál az S S O Setup dokumentációs hivatkozását tartalmazza.](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel és a válasz URL-címével. Az értékek lekéréséhez forduljon a CloudPassage ügyfélszolgálati [csapatához](https://www.cloudpassage.com/company/contact/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A CloudPassage alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)

1. A fentiek mellett a CloudPassage alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre is fel vannak töltve, de a követelménynek megfelelően áttekintheti őket.

    | Name (Név) | Forrás attribútum|
    | ---------------| --------------- |
    | FirstName |User. givenName |
    | LastName |felhasználó. vezetéknév |
    | e-mail |User. mail |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **CloudPassage beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a CloudPassage.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **CloudPassage**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-cloudpassage-sso"></a>CloudPassage SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a CloudPassage vállalati webhelyre rendszergazdaként.

1. A felső menüben kattintson a **Beállítások**, majd a **Hely felügyelete**elemre. 
   
    ![Képernyőfelvétel: a CloudPassage hely van kiválasztva.][12]

1. Kattintson a **hitelesítési beállítások** fülre. 
   
    ![Képernyőfelvétel: a CloudPassage webhely, amelyen a hitelesítési beállítások lap van kiválasztva.][13]

1. Az **egyszeri bejelentkezés beállításai** szakaszban hajtsa végre a következő lépéseket: 
   
    ![Képernyőfelvétel: az egyszeri bejelentkezés beállításai szakasz, ahol megadhatja az ebben a lépésben szereplő információkat.][14]

    a. Jelölje be az **egyszeri bejelentkezés engedélyezése (SSO) (SSO beállítási dokumentáció)** jelölőnégyzetet.
    
    b. Illessze be az **Azure ad-azonosítót** az **SAML kiállító URL-** szövegmezőbe.
  
    c. Illessze be a **bejelentkezési URL-címet** az **SAML-végpont URL-** szövegmezőbe.
  
    d. Illessze be a **kijelentkezési URL-címet** a **kijelentkezési oldal** szövegmezőbe.
  
    e. Nyissa meg a letöltött tanúsítványt a Jegyzettömbben, másolja a letöltött tanúsítvány tartalmát a vágólapra, majd illessze be az **x 509 tanúsítvány** szövegmezőbe.
  
    f. Kattintson a **Mentés** gombra.

### <a name="create-cloudpassage-test-user"></a>CloudPassage-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy B. Simon nevű felhasználó létrehozása a CloudPassage-ben.

**Ha B. Simon nevű felhasználót szeretne létrehozni a CloudPassage-ben, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **CloudPassage** vállalati webhelyre rendszergazdaként. 

1. A felső eszköztáron kattintson a **Beállítások**, majd a **Hely felügyelete**elemre. 
   
    ![Képernyőfelvétel: a CloudPassage kiválasztása a hely felügyeletével.][22] 

1. Kattintson a **felhasználók** lapra, majd az **új felhasználó hozzáadása**lehetőségre. 
   
    ![Képernyőfelvétel: a CloudPassage megjelenítése a felhasználók lapon, és az új felhasználó hozzáadása lehetőség.][23]

1. Az **új felhasználó hozzáadása** szakaszban hajtsa végre a következő lépéseket: 
   
    ![Képernyőfelvétel: az új felhasználó hozzáadása szakasz, ahol megadhatja a felhasználói adatokat.][24]
    
    a. A **Utónév** szövegmezőbe írja be a következőt: Britta. 
  
    b. A vezetéknév szövegmezőbe írja be a Simon **nevet** .
  
    c. A **Felhasználónév** szövegmezőben, az **e-mail** szövegmezőben és az **e-mail** szövegmezőben írja be a Britta felhasználónevét az Azure ad-be.
  
    d. A **hozzáférés típusa**beállításnál válassza a **Halo Portal-hozzáférés engedélyezése**lehetőséget.
  
    e. Kattintson a **Hozzáadás** parancsra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a CloudPassage csempére kattint, automatikusan be kell jelentkeznie arra a CloudPassage, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A CloudPassage kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

