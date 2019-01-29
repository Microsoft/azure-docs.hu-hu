---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező BitaBIZ |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és BitaBIZ között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 894363a48f0ba1f45664451d5508473f78aceb07
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162208"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező BitaBIZ

Ebben az oktatóanyagban elsajátíthatja, hogyan BitaBIZ integrálása az Azure Active Directory (Azure AD).

BitaBIZ integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá BitaBIZ Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett BitaBIZ (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

BitaBIZ az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy BitaBIZ egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. BitaBIZ hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-bitabiz-from-the-gallery"></a>BitaBIZ hozzáadása a katalógusból
Az Azure AD integrálása a BitaBIZ konfigurálásához hozzá kell BitaBIZ a katalógusból a felügyelt SaaS-alkalmazások listájára.

**BitaBIZ hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **BitaBIZ**válassza **BitaBIZ** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában BitaBIZ](./media/bitabiz-tutorial/tutorial_bitabiz_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés BitaBIZ a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó BitaBIZ mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó BitaBIZ hivatkozás kapcsolata kell létrehozni.

BitaBIZ, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az BitaBIZ tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy BitaBIZ tesztfelhasználót](#create-a-bitabiz-test-user)**  – egy megfelelője a Britta Simon BitaBIZ, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és BitaBIZ alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés BitaBIZ, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **BitaBIZ** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/bitabiz-tutorial/tutorial_bitabiz_samlbase.png)

1. Az a **BitaBIZ tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás Identitásszolgáltató által kezdeményezett módban konfigurálása:

    ![BitaBIZ tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/bitabiz-tutorial/tutorial_bitabiz_url.png)

    Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.bitabiz.com/<instanceId>`

    > [!NOTE] 
    > A fenti URL-cím értéke csak bemutatásához. Frissítse az értéket a tényleges azonosítója, amelynek az ismertetése az oktatóanyag későbbi részében.

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![BitaBIZ tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/bitabiz-tutorial/tutorial_bitabiz_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím: `https://www.bitabiz.com/dashboard`

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/bitabiz-tutorial/tutorial_bitabiz_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/bitabiz-tutorial/tutorial_general_400.png)
    
1. Az a **BitaBIZ konfigurációs** területén kattintson **konfigurálása BitaBIZ** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![BitaBIZ konfiguráció](./media/bitabiz-tutorial/tutorial_bitabiz_configure.png) 

1. Egy másik böngészőablakban, a bejelentkezés a BitaBIZ bérlői rendszergazdaként.

1. Kattintson a **telepítő rendszergazda**.

    ![BitaBIZ konfiguráció](./media/bitabiz-tutorial/settings1.png)

1. Kattintson a **Microsoft Integrációk** alatt **érték hozzáadása** szakaszban.

    ![BitaBIZ konfiguráció](./media/bitabiz-tutorial/settings2.png)

1. Görgessen le a szakasz **a Microsoft Azure ad-ben (engedélyezése az egyszeri bejelentkezés)** , és hajtsa végre a következő lépéseket:

    ![BitaBIZ konfiguráció](./media/bitabiz-tutorial/settings3.png)

    a. Másolja az értéket a **Entitásazonosító ("-azonosító" az Azure ad-ben)** szövegmezőbe, és illessze be azt a **azonosítója** a szövegmezőbe a **BitaBIZ tartomány és URL-címek** szakaszban az Azure Portalon. 
    
    b. Az a **az Azure AD egyszeri bejelentkezési szolgáltatás URL-cím** szövegmezőjébe illessze be **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.
    
    c. Az a **Azure AD SAML Entitásazonosító** szövegmezőjébe illessze be **SAML Entitásazonosító**, az Azure Portalról másolt.

    d. Nyissa meg a letöltött **Certificate(Base64)** fájlt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **az Azure AD aláíró tanúsítvány (Base64-kódolású)** szövegmezőbe.

    e. Adja hozzá a vállalati e-mail-tartomány, nevezze el a mycompany.com **tartománynév** SSO hozzárendelése a felhasználók a vállalati e-mailben ezt a tartományt a szövegmezőben (nem kötelező).
    
    f. Megjelölés **egyszeri bejelentkezés engedélyezve** BitaBIZ fiók.
    
    g. Kattintson a **az Azure AD-konfiguráció mentéséhez** mentéséhez és az egyszeri bejelentkezési konfiguráció aktiválásához.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/bitabiz-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/bitabiz-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/bitabiz-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/bitabiz-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-bitabiz-test-user"></a>BitaBIZ tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók BitaBIZ jelentkezzen be, akkor ki kell építeni BitaBIZ be.  
BitaBIZ, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a BitaBIZ vállalati webhely.

1. Kattintson a **telepítő rendszergazda**.

    ![BitaBIZ felhasználó hozzáadása](./media/bitabiz-tutorial/settings1.png)

1. Kattintson a **felhasználók hozzáadása** alatt **szervezet** szakaszban.

    ![BitaBIZ felhasználó hozzáadása](./media/bitabiz-tutorial/user1.png)

1. Kattintson a **hozzáadása új alkalmazott**.

    ![BitaBIZ felhasználó hozzáadása](./media/bitabiz-tutorial/user2.png)

1. Az a **"Hozzáadása új alkalmazott"** párbeszédpanel lapon, a következő lépésekkel:

    ![BitaBIZ felhasználó hozzáadása](./media/bitabiz-tutorial/user3.png)

    a. Az a **Utónév** szövegmezőbe írja be az első felhasználóneve Britta például.

    b. Az a **Vezetéknév** szövegmezőbe írja be a Simon például a felhasználó vezetékneve.

    c. Az a **E-mail** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.

    d. Válassza ki a dátumot a **alkalmazási dátum**.

    e. Nincsenek egyéb nem kötelező felhasználói attribútumokat, amelyek a felhasználó beállíthatja. Tekintse meg a [alkalmazottak telepítés Doc](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) további részletekért.    
    
    f. Kattintson a **mentés alkalmazott**.
    
    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a egy hivatkozást, mielőtt aktívvá válik, győződjön meg arról, hogy fiókjuk követi.
    
### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés BitaBIZ Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel BitaBIZ, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **BitaBIZ**.

    ![Az alkalmazások listáját a BitaBIZ hivatkozásra](./media/bitabiz-tutorial/tutorial_bitabiz_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a BitaBIZ csempére kattint, meg kell lekérése automatikusan bejelentkezett az BitaBIZ alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bitabiz-tutorial/tutorial_general_01.png
[2]: ./media/bitabiz-tutorial/tutorial_general_02.png
[3]: ./media/bitabiz-tutorial/tutorial_general_03.png
[4]: ./media/bitabiz-tutorial/tutorial_general_04.png

[100]: ./media/bitabiz-tutorial/tutorial_general_100.png

[200]: ./media/bitabiz-tutorial/tutorial_general_200.png
[201]: ./media/bitabiz-tutorial/tutorial_general_201.png
[202]: ./media/bitabiz-tutorial/tutorial_general_202.png
[203]: ./media/bitabiz-tutorial/tutorial_general_203.png

