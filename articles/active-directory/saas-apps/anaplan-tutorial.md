---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Anaplan |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Anaplan között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4a9c2914-6c8c-4a88-93e3-3753afb40e6b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jeedes
ms.openlocfilehash: c2a7e6654d1bae1cb8db56d33e1bd7856c1c4257
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812624"
---
# <a name="tutorial-azure-active-directory-integration-with-anaplan"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Anaplan

Ebben az oktatóanyagban elsajátíthatja, hogyan Anaplan integrálása az Azure Active Directory (Azure AD).

Anaplan integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Anaplan Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Anaplan (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Anaplan az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Anaplan egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Anaplan hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-anaplan-from-the-gallery"></a>Anaplan hozzáadása a katalógusból
Az Azure AD integrálása a Anaplan konfigurálásához hozzá kell Anaplan a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Anaplan hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **Anaplan**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/anaplan-tutorial/tutorial_anaplan_search.png)

5. Az eredmények panelen válassza ki a **Anaplan**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/anaplan-tutorial/tutorial_anaplan_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó Anaplan az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Anaplan mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Anaplan hivatkozás kapcsolata kell létrehozni.

Anaplan, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Anaplan tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Egy Anaplan tesztfelhasználó létrehozása](#creating-an-anaplan-test-user)**  – egy megfelelője a Britta Simon Anaplan, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Anaplan alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Anaplan, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Anaplan** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/anaplan-tutorial/tutorial_anaplan_samlbase.png)

3. Az a **Anaplan tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/anaplan-tutorial/tutorial_anaplan_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://sdp.anaplan.com/frontdoor/saml/<tenant name>`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.anaplan.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [Anaplan ügyfél-támogatási csapatának](mailto:support@anaplan.com) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/anaplan-tutorial/tutorial_anaplan_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/anaplan-tutorial/tutorial_general_400.png)

6. Az a **Anaplan konfigurációs** területén kattintson **konfigurálása Anaplan** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/anaplan-tutorial/tutorial_anaplan_configure.png) 

7. Az egyszeri bejelentkezés konfigurálása **Anaplan** oldalon kell küldenie a letöltött **metaadatainak XML**, **SAML Entitásazonosító**, **SAML egyszeri bejelentkezési szolgáltatás URL-cím** és **kijelentkezéses URL-cím** való [Anaplan támogatási csapatának](mailto:support@anaplan.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/anaplan-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/anaplan-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/anaplan-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/anaplan-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-anaplan-test-user"></a>Egy Anaplan tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Anaplan hoz létre. Együttműködve [Anaplan támogatási csapatának](mailto:support@anaplan.com) a felhasználók hozzáadása az Anaplan platformon.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Anaplan Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Anaplan, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Anaplan**.

    ![Egyszeri bejelentkezés konfigurálása](./media/anaplan-tutorial/tutorial_anaplan_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Anaplan csempére kattint, meg kell lekérése automatikusan bejelentkezett az Anaplan alkalmazáshoz.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/anaplan-tutorial/tutorial_general_01.png
[2]: ./media/anaplan-tutorial/tutorial_general_02.png
[3]: ./media/anaplan-tutorial/tutorial_general_03.png
[4]: ./media/anaplan-tutorial/tutorial_general_04.png

[100]: ./media/anaplan-tutorial/tutorial_general_100.png

[200]: ./media/anaplan-tutorial/tutorial_general_200.png
[201]: ./media/anaplan-tutorial/tutorial_general_201.png
[202]: ./media/anaplan-tutorial/tutorial_general_202.png
[203]: ./media/anaplan-tutorial/tutorial_general_203.png

