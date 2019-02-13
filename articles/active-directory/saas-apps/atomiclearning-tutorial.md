---
title: 'Oktatóanyag: Az Azure Active Directory-integráció atomi tanulással |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az atomi Learning között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 495f54a6-e6c4-41b0-aafa-a6283d33efc8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74df7bd7073716dbbb372a43af809bf56c6cd32c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209285"
---
# <a name="tutorial-azure-active-directory-integration-with-atomic-learning"></a>Oktatóanyag: Az atomi Learning az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan atomi Learning integrálása az Azure Active Directory (Azure AD).

Atomi Learning integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá atomi Learning az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett atomi Learning (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása atomi tanulással, a következőkre van szükség:

- Azure AD-előfizetés
- Egy atomi Learning egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Atomi Learning hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-atomic-learning-from-the-gallery"></a>Atomi Learning hozzáadása a katalógusból
Atomi Learning integrálása az Azure AD beállítása, hozzá kell atomi Learning a galériából a felügyelt SaaS-alkalmazások listájára.

**Atomi Learning hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **atomi Learning**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/atomiclearning-tutorial/tutorial_atomiclearning_search.png)

1. Az eredmények panelen válassza ki a **atomi Learning**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/atomiclearning-tutorial/tutorial_atomiclearning_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a teszt "Britta Simon." nevű felhasználó atomi tanulással

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó atomi Learning mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és az atomi Learning a kapcsolódó felhasználó közötti kapcsolat kapcsolatot kell létrehozni.

Atomi Learning rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés atomi tanulással tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Egy atomi Learning tesztfelhasználó létrehozása](#creating-an-atomic-learning-test-user)**  – egy megfelelője a Britta Simon atomi Learning, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezése az Azure AD egyszeri bejelentkezés az Azure Portalon, és az atomi Learning alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása atomi tanulással, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **atomi Learning** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/atomiclearning-tutorial/tutorial_atomiclearning_samlbase.png)

1. Az a **atomi Learning tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/atomiclearning-tutorial/tutorial_atomiclearning_url.png)

     Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://secure2.atomiclearning.com/sso/shibboleth/<companyname>`
    
    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [atomi Learning ügyfél-támogatási csapatának](mailto:cs@atomiclearning.com) lekérni ezt az értéket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/atomiclearning-tutorial/tutorial_atomiclearning_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/atomiclearning-tutorial/tutorial_general_400.png)

1. Az egyszeri bejelentkezés konfigurálása **atomi Learning** oldalon kell küldenie a letöltött **metaadatainak XML** való [atomi Learning támogatási csoport](mailto:cs@atomiclearning.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/atomiclearning-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/atomiclearning-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/atomiclearning-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/atomiclearning-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-atomic-learning-test-user"></a>Egy atomi Learning tesztfelhasználó létrehozása

Ebben a szakaszban egy atomi Learning Britta Simon nevű felhasználó hoz létre. Atomi Learning támogatja just-in-time-kiépítés, amely alapértelmezés szerint van engedélyezve. 

Nincs meg ebben a szakaszban a művelet elem. Új felhasználó atomi Learning eléréséhez, ha még nem létezik, még a felhasználó az az e-mail-cím használatával tett kísérlet során jön létre.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával atomi Learning Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel atomi tanulás, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **atomi Learning**.

    ![Egyszeri bejelentkezés konfigurálása](./media/atomiclearning-tutorial/tutorial_atomiclearning_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a atomi Learning csempére kattint, akkor kell lekérése automatikusan bejelentkezett az atomi Learning alkalmazásához.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/atomiclearning-tutorial/tutorial_general_01.png
[2]: ./media/atomiclearning-tutorial/tutorial_general_02.png
[3]: ./media/atomiclearning-tutorial/tutorial_general_03.png
[4]: ./media/atomiclearning-tutorial/tutorial_general_04.png

[100]: ./media/atomiclearning-tutorial/tutorial_general_100.png

[200]: ./media/atomiclearning-tutorial/tutorial_general_200.png
[201]: ./media/atomiclearning-tutorial/tutorial_general_201.png
[202]: ./media/atomiclearning-tutorial/tutorial_general_202.png
[203]: ./media/atomiclearning-tutorial/tutorial_general_203.png

