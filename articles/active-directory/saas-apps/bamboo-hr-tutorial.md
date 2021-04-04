---
title: 'Oktatóanyag: Azure Active Directory integráció a BambooHR-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és BambooHR között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: 3926e98cb55d1afd80caf4af5d67910b66c09cdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180336"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Oktatóanyag: Azure Active Directory integráció a BambooHR

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a BambooHR a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az BambooHR-t az Azure AD-vel, a következőket teheti:

* A BambooHR-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a BambooHR az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* BambooHR egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A BambooHR támogatja az **SP** által KEZDEMÉNYEZett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.


## <a name="adding-bamboohr-from-the-gallery"></a>BambooHR hozzáadása a gyűjteményből

A BambooHR Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a BambooHR a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **BambooHR** kifejezést a keresőmezőbe.
1. Válassza ki a **BambooHR** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-bamboohr"></a>Azure AD SSO konfigurálása és tesztelése a BambooHR-hez

Konfigurálja és tesztelje az Azure AD SSO-t a BambooHR a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a BambooHR-ben.

Az Azure AD SSO és a BambooHR konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    * **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. **[BAMBOOHR SSO konfigurálása](#configure-bamboohr-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre BambooHR-teszt felhasználót](#create-bamboohr-test-user)** – hogy a BambooHR Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **BambooHR** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<company>.bamboohr.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | Válasz URL-cím |
    |-----------|
    | `https://<company>.bamboohr.com/saml/consume.php` |
    | `https://<company>.bamboohr.co.uk/saml/consume.php` |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és a válasz URL-címével. Az értékek lekéréséhez forduljon a BambooHR ügyfélszolgálati [csapatához](https://www.bamboohr.com/contact.php) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **BambooHR beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a BambooHR.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **BambooHR** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-bamboohr-sso"></a>BambooHR SSO konfigurálása

1. Egy új ablakban jelentkezzen be a BambooHR vállalati webhelyre rendszergazdaként.

2. A kezdőlapon tegye a következőket:
   
    ![Az BambooHR egyetlen Sign-On lapja](./media/bamboo-hr-tutorial/ic796691.png "Egyszeri bejelentkezés")   

    a. Válassza az **alkalmazások** lehetőséget.
   
    b. Az **alkalmazások** ablaktáblán válassza az **egyszeri bejelentkezés** lehetőséget.
   
    c. Válassza **az SAML egyszeri bejelentkezés** lehetőséget.

3. Az **SAML egyszeri bejelentkezés** panelen tegye a következőket:
   
    ![Az SAML egyetlen Sign-On panelje](./media/bamboo-hr-tutorial/IC796692.png "SAML egyszeri Sign-On")
   
    a. Az **SSO bejelentkezési URL-címe** mezőbe illessze be a 6. lépésben a Azure Portalból másolt **bejelentkezési URL-címet** .
      
    b. Nyissa meg a Jegyzettömbben a Azure Portal letöltött Base-64 kódolású tanúsítványt, másolja a tartalmát, majd illessze be az **X. 509 tanúsítvány** mezőbe.
   
    c. Válassza a **Mentés** lehetőséget.

### <a name="create-bamboohr-test-user"></a>BambooHR-tesztelési felhasználó létrehozása

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezzenek a BambooHR, manuálisan állítsa be őket a BambooHR-ben a következő módon:

1. Jelentkezzen be a **BambooHR** -webhelyre rendszergazdaként.

2. A felső eszköztáron válassza a **Beállítások** lehetőséget.
   
    ![A beállítások gomb](./media/bamboo-hr-tutorial/IC796694.png "Beállítás")

3. Válassza az **Áttekintés** lehetőséget.

4. A bal oldali ablaktáblán válassza a **biztonsági**  >  **felhasználók** lehetőséget.

5. Írja be a beállítani kívánt érvényes Azure AD-fiók felhasználónevét, jelszavát és e-mail-címét.

6. Kattintson a **Mentés** gombra.
        
>[!NOTE]
>Az Azure AD felhasználói fiókjainak beállításához használhatja a BambooHR felhasználói fiókok létrehozásához használható eszközöket vagy API-kat is.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

1. Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a BambooHR bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

2. Lépjen közvetlenül a BambooHR bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

3. Használhatja a Microsoft Access panelt. Ha a hozzáférési panelen a BambooHR csempére kattint, a rendszer átirányítja a BambooHR bejelentkezési URL-címére. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Következő lépések

A BambooHR konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).