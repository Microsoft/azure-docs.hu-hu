---
title: 'Oktatóanyag: Azure Active Directory integráció a Freshdeskbe-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Freshdeskbe között.
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
ms.openlocfilehash: e1394eafdfd733b5d69a4d4abbb6b218b4c8c10d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651904"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Oktatóanyag: Azure Active Directory integráció a Freshdeskbe

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Freshdeskbe a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Freshdeskbe-t az Azure AD-vel, a következőket teheti:

* A Freshdeskbe-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Freshdeskbe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:
 
* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Freshdeskbe egyszeri bejelentkezésre (SSO) alkalmas előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Freshdeskbe támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="add-freshdesk-from-the-gallery"></a>Freshdeskbe hozzáadása a gyűjteményből

A Freshdeskbe Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Freshdeskbe a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **freshdeskbe** kifejezést a keresőmezőbe.
1. Válassza ki a **freshdeskbe** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>Azure AD SSO konfigurálása és tesztelése a Freshdeskbe-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Freshdeskbe a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Freshdeskbe-ben.

Az Azure AD SSO és a Freshdeskbe konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
1. **[FRESHDESKBE SSO konfigurálása](#configure-freshdesk-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre freshdeskbe-teszt felhasználót](#create-freshdesk-test-user)** – hogy a freshdeskbe Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

1. A Azure Portal **freshdeskbe** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő mintával: `https://<tenant-name>.freshdesk.com` vagy bármely más, a freshdeskbe által javasolt érték.

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő mintával: `https://<tenant-name>.freshdesk.com` vagy bármely más, a freshdeskbe által javasolt érték.
     
    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenant-name>.freshdesk.com/login/saml`
    
    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Az értékek lekéréséhez forduljon a Freshdeskbe ügyfélszolgálati [csapatához](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Freshdeskbe alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, míg az **egyedi felhasználói azonosító** a **User. userPrincipalName** van leképezve, de a freshdeskbe azt várja, hogy a jogcím a **User. mail** használatával legyen leképezve, ezért az attribútum-hozzárendelés szerkesztéséhez kattintson a Szerkesztés ikonra, és módosítsa az attribútumok leképezését.

    ![image](common/edit-attribute.png)

1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Freshdeskbe beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Freshdeskbe.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **freshdeskbe** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-freshdesk-sso"></a>Freshdeskbe SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Freshdeskbe vállalati webhelyre rendszergazdaként.

2. Válassza a **Biztonság ikont** , és a **Biztonság** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés](./media/freshdesk-tutorial/configure-1.png "Egyszeri bejelentkezés")
  
    a. Az **egyszeri bejelentkezéshez** válassza **a be** lehetőséget.

    b. A **login metódusban** válassza az **SAML egyszeri bejelentkezés** lehetőséget.

    c. Az identitásszolgáltató szövegmezőben **megadott entitás-azonosítóban** illessze be az **entitás-azonosító** értékét, amelyet a Azure Portal másolt.

    d. Az **SAML SSO URL** szövegmezőbe illessze be a **bejelentkezési URL** értékét, amelyet a Azure Portal másolt.

    e. Az **aláírási beállítások** területen jelölje be a **csak az aláírt érvényesítések** lehetőséget a legördülő listából.

    f. A **kijelentkezési URL** szövegmezőben illessze be a **kijelentkezési URL-** értéket, amelyet a Azure Portal másolt.

    : A **biztonsági tanúsítvány** szövegmezőbe illessze be a korábban beszerzett **tanúsítvány (Base64)** értékét.
  
    h. Kattintson a **Mentés** gombra.

## <a name="create-freshdesk-test-user"></a>Freshdeskbe-tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Freshdeskbe, a Freshdeskbe-ben kell kiépíteni őket.  
Freshdeskbe esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **freshdeskbe** -bérlőbe.

1. A bal oldali menüben kattintson a **rendszergazda** elemre, majd az **általános beállítások** lapon kattintson az **ügynökök** elemre.
  
    ![Ügynökök](./media/freshdesk-tutorial/create-user-1.png "Ügynökök")

1. Kattintson az **új ügynök** elemre.

    ![Új ügynök](./media/freshdesk-tutorial/create-user-2.png "Új ügynök")

1. Az ügynök adatai párbeszédpanelen adja meg a szükséges mezőket, és kattintson az **ügynök létrehozása** lehetőségre.

    ![Ügynök adatai](./media/freshdesk-tutorial/create-user-3.png "Ügynök adatai")

    >[!NOTE]
    >Az Azure AD-fiók tulajdonosa egy e-mailt fog kapni, amely tartalmaz egy hivatkozást, amely a fiók aktiválását megelőzően megerősíti a fiókot.
    >
    >[!NOTE]
    >A Freshdeskbe által biztosított bármely más Freshdeskbe felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat az Azure AD felhasználói fiókjainak Freshdeskbe való kiépítéséhez.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Freshdeskbe bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Freshdeskbe bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások Freshdeskbe csempére kattint, automatikusan be kell jelentkeznie arra a Freshdeskbe, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A Freshdeskbe konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).