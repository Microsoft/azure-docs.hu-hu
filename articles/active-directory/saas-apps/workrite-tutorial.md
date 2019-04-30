---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Workrite |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Workrite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 2a5c2956-a011-4d5c-877b-80679b6587b5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb64debca10cf7be6e2e328a1f401f125b67d940
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098507"
---
# <a name="tutorial-azure-active-directory-integration-with-workrite"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Workrite

Ebben az oktatóanyagban elsajátíthatja, hogyan Workrite integrálása az Azure Active Directory (Azure AD).

Workrite integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Workrite Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Workrite (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Workrite az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Workrite egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Workrite hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-workrite-from-the-gallery"></a>Workrite hozzáadása a katalógusból
Az Azure AD integrálása a Workrite konfigurálásához hozzá kell Workrite a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Workrite hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Workrite**válassza **Workrite** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Workrite](./media/workrite-tutorial/tutorial_workrite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Workrite a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Workrite mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Workrite hivatkozás kapcsolata kell létrehozni.

Workrite, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Workrite tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Workrite tesztfelhasználót](#create-a-workrite-test-user)**  – egy megfelelője a Britta Simon Workrite, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Workrite alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Workrite, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Workrite** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/workrite-tutorial/tutorial_workrite_samlbase.png)

1. Az a **Workrite tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Workrite tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/workrite-tutorial/tutorial_workrite_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://app.workrite.co.uk/securelogin/samlgateway.aspx?id=<uniqueid>`

    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Workrite ügyfél-támogatási csapatának](mailto:support@workrite.co.uk) lekérni ezt az értéket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/workrite-tutorial/tutorial_workrite_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/workrite-tutorial/tutorial_general_400.png)

1. Az a **Workrite konfigurációs** területén kattintson **konfigurálása Workrite** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Workrite konfiguráció](./media/workrite-tutorial/tutorial_workrite_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **Workrite** oldalon kell küldenie a letöltött **Certificate(Base64), kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** való [Workrite támogatási csoport](mailto:support@workrite.co.uk).

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/workrite-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/workrite-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/workrite-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/workrite-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-workrite-test-user"></a>Workrite tesztfelhasználó létrehozása

Ez a szakasz célja Workrite Britta Simon nevű felhasználó létrehozásához.

**Britta Simon Workrite nevű felhasználó létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a workrite vállalati webhely.

1. Kattintson a navigációs ablaktáblában **rendszergazdai**.
   
    ![Rendszergazdai vezérlés][400]

1. Ugrás a gyors hivatkozásokat, és kattintson **hozzon létre egy felhasználót**.
   
    ![Hozzon létre a felhasználó szakaszról][401]

1. Az a **Create User** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Felhasználói párbeszédpanel létrehozása][402]
    
    a. Az a **E-mail** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.

    b. Az a **Utónév** szövegmezőbe írja be a felhasználó például Britta firstname.

    c. Az a **Vezetéknév** szövegmezőbe írja be például a Simon a felhasználó vezetékneve.
    
    d. Válassza ki **ügyfél rendszergazda** , **munkakör kiválasztása**.
    
    e. Kattintson a **Save** (Mentés) gombra.   

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Workrite Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Workrite, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Workrite**.

    ![Az alkalmazások listáját a Workrite hivatkozásra](./media/workrite-tutorial/tutorial_workrite_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.

Ha a hozzáférési panelen a Workrite csempére kattint, meg kell lekérése automatikusan bejelentkezett az Workrite alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/workrite-tutorial/tutorial_general_01.png
[2]: ./media/workrite-tutorial/tutorial_general_02.png
[3]: ./media/workrite-tutorial/tutorial_general_03.png
[4]: ./media/workrite-tutorial/tutorial_general_04.png

[100]: ./media/workrite-tutorial/tutorial_general_100.png

[200]: ./media/workrite-tutorial/tutorial_general_200.png
[201]: ./media/workrite-tutorial/tutorial_general_201.png
[202]: ./media/workrite-tutorial/tutorial_general_202.png
[203]: ./media/workrite-tutorial/tutorial_general_203.png
[400]: ./media/workrite-tutorial/tutorial_workrite_400.png
[401]: ./media/workrite-tutorial/tutorial_workrite_401.png
[402]: ./media/workrite-tutorial/tutorial_workrite_402.png

