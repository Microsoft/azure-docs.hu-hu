---
title: 'Oktatóanyag: Azure Active Directory integráció a Jobscience-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Jobscience között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 5a104dcd6ccf500c115359a1b72c67b85359a802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002187"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Oktatóanyag: Azure Active Directory integráció a Jobscience

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Jobscience a Azure Active Directory (Azure AD) szolgáltatással.

A Jobscience és az Azure AD integrálásával a következő előnyöket nyújtja:

- Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Jobscience
- Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkeznek a Jobscience (egyszeri bejelentkezés) az Azure AD-fiókkal
- A fiókokat egyetlen központi helyen kezelheti – a Azure Portal

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Jobscience való konfigurálásához a következő elemek szükségesek:

- Azure AD-előfizetés
- Jobscience egyszeri bejelentkezésre engedélyezett előfizetés

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem javasoljuk éles környezet használatát.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi javaslatokat:

- Ne használja éles környezetét, ha szükséges.
- Ha nem rendelkezik Azure AD-próbaverzióval, egy hónapos próbaverziót is beszerezhet itt: [próbaverziós ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését teszteli a tesztelési környezetben. Az oktatóanyagban ismertetett forgatókönyv két fő építőelemet tartalmaz:

1. Jobscience hozzáadása a gyűjteményből
1. Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

## <a name="adding-jobscience-from-the-gallery"></a>Jobscience hozzáadása a gyűjteményből
A Jobscience Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Jobscience a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Jobscience szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazásokhoz**. Ezután nyissa meg az **összes alkalmazást**.

    ![A képernyőképen a felügyelet alatt kiválasztott Azure Portal vállalati alkalmazások láthatók, és minden alkalmazás ki van választva.][2]
    
1. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![A képernyőképen a kiválasztott új alkalmazás látható.][3]

1. A keresőmezőbe írja be a **Jobscience** kifejezést.

    ![Képernyőfelvétel: a jobscience megadott gyűjteményből származó Hozzáadás a katalógusból.](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. Az eredmények panelen válassza a **Jobscience** lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Képernyőfelvétel: a Jobscience által tartalmazott eredmények.](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése
Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Jobscience-mel konfigurálja és teszteli a "Britta Simon" nevű tesztelési felhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD-nek tudnia kell, hogy mi a Jobscience felhasználója az Azure AD-ben. Más szóval az Azure AD-felhasználó és a Jobscience kapcsolódó felhasználó közötti kapcsolati kapcsolat létesítésére van szükség.

A Jobscience-ben rendelje hozzá a **Felhasználónév értékét** az Azure ad-ben a kapcsolati kapcsolat létrehozásához használt **Felhasználónév** értékeként.

Az Azure AD egyszeri bejelentkezés Jobscience való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
1. **[Azure ad-felhasználó létrehozása](#creating-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
1. **[Jobscience-teszt felhasználó létrehozása](#creating-a-jobscience-test-user)** – ahhoz, hogy a Jobscience Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
1. **[Az Azure ad-teszt felhasználójának kiosztása](#assigning-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portalban, és konfigurálhatja az egyszeri bejelentkezést a Jobscience alkalmazásban.

**Az Azure AD egyszeri bejelentkezés Jobscience való konfigurálásához hajtsa végre a következő lépéseket:**

1. A Azure Portal **Jobscience** alkalmazás-integráció lapján kattintson az **egyszeri bejelentkezés** elemre.

    ![Képernyőfelvétel: az Azure Portal a kezelés elemnél kiválasztott egyszeri bejelentkezést jeleníti meg.][4]

1. Az **egyszeri** Bejelentkezés párbeszédpanelen válassza a **mód**    **SAML-alapú bejelentkezés** lehetőséget az egyszeri bejelentkezés engedélyezéséhez.
 
    ![A képernyőfelvételen az SAML-alapú bejelentkezési mód van kiválasztva.](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. A **Jobscience tartomány és URL-címek** szakaszban hajtsa végre a következő lépéseket:

    ![A képernyőképen a bejelentkezési U R L látható.](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával:  `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Ez az érték nem valós. Frissítse ezt az értéket a tényleges Sign-On URL-címmel. Ezt az értéket a [Jobscience-ügyfél támogatási csapata](http://www.jobscience.com/support) vagy a létrehozandó egyszeri bejelentkezési profil alapján szerezheti be, amelyet később az oktatóanyagban talál. 
 
1. Az **SAML aláíró tanúsítvány** szakaszban kattintson a **tanúsítvány (Base64)** elemre, majd mentse a tanúsítványt a számítógépre.

    ![Képernyőfelvétel: az SAML aláíró tanúsítvány panel, ahol letöltheti a tanúsítványokat.](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Kattintson a **Save (Mentés** ) gombra.

    ![A képernyőképen a Mentés gomb látható.](./media/jobscience-tutorial/tutorial_general_400.png)

1. A **Jobscience-konfiguráció** szakaszban kattintson a **Jobscience konfigurálása** elemre a **Bejelentkezés konfigurálása** ablak megnyitásához. Másolja a kijelentkezési **URL-címet, az SAML-entitás azonosítóját és az SAML-Sign-On szolgáltatás URL-címét** a **rövid útmutató szakaszban.**

    ![A képernyőképen a Jobscience konfigurációs ablak látható.](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Jelentkezzen be a Jobscience vállalati webhelyre rendszergazdaként.

1. Nyissa meg a **telepítőt**.
   
   ![A képernyőfelvételen a vállalat telepítési eleme látható.](./media/jobscience-tutorial/IC784358.png "Beállítás")

1. A bal oldali navigációs ablaktábla **felügyelet** szakaszában kattintson a **tartományi felügyelet** elemre a kapcsolódó szakasz kibontásához, majd kattintson a **saját tartomány** lehetőségre a **saját tartomány** lap megnyitásához. 
   
   ![Saját tartomány](./media/jobscience-tutorial/ic767825.png "Saját tartomány")

1. Annak ellenőrzéséhez, hogy a tartomány megfelelően van-e beállítva, győződjön meg arról, hogy a "**4. lépés központi telepítése a felhasználók számára**" értékre van állítva, és tekintse át a "**saját tartomány beállításai**" című részt.

    ![A felhasználó számára központilag telepített tartomány](./media/jobscience-tutorial/ic784377.png "A felhasználó számára központilag telepített tartomány")

1. A Jobscience vállalat webhelyén kattintson a **biztonsági vezérlők** elemre, majd kattintson az **egyszeres Sign-On beállítások** elemre.
    
    ![Képernyőfelvétel: a biztonsági vezérlőkből kiválasztott egyszeres Sign-On beállítások.](./media/jobscience-tutorial/ic784364.png "Biztonsági vezérlők")

1. Az **egyszeres Sign-On beállítások** szakaszban hajtsa végre a következő lépéseket:
    
    ![Egyszeres Sign-On beállítások](./media/jobscience-tutorial/ic781026.png "Egyszeres Sign-On beállítások")
    
    a. Válassza az **SAML engedélyezve** lehetőséget.

    b. Kattintson az **új** gombra.

1. Az **SAML egyszeri Sign-On beállítás szerkesztése** párbeszédpanelen hajtsa végre a következő lépéseket:
    
    ![SAML egyszeri Sign-On beállítás](./media/jobscience-tutorial/ic784365.png "SAML egyszeri Sign-On beállítás")
    
    a. A **név** szövegmezőbe írja be a konfiguráció nevét.

    b. A **kiállító** szövegmezőbe illessze be a SAML- **entitás azonosítójának** értékét, amelyet a Azure Portalból másolt.

    c. Az **entitás-azonosító** szövegmezőbe írja be a következőt: `https://salesforce-jobscience.com`

    d. Az Azure AD-tanúsítvány feltöltéséhez kattintson a **Tallózás** gombra.

    e. **SAML-identitás típusaként** válassza **az érvényesítés az összevonási azonosítót tartalmazza a felhasználói objektumból** lehetőséget.

    f. **SAML-identitás helyeként** válassza az **identitás elemet a tulajdonos utasítás NameIdentfier elemében**.

    : Az **Identity Provider bejelentkezési URL-címe** szövegmezőbe illessze be az **SAML Sign-On szolgáltatás URL-címének** értékét, amelyet a Azure Portalból másolt.

    h. Az **Identity Provider kijelentkezési URL-címe** szövegmezőbe illessze be a kijelentkezési **URL-címet**, amelyet a Azure Portalból másolt.

    i. Kattintson a **Mentés** gombra.

1. A bal oldali navigációs ablaktábla **felügyelet** szakaszában kattintson a **tartományi felügyelet** elemre a kapcsolódó szakasz kibontásához, majd kattintson a **saját tartomány** lehetőségre a **saját tartomány** lap megnyitásához. 
    
    ![Saját tartomány](./media/jobscience-tutorial/ic767825.png "Saját tartomány")

1. A **saját tartomány** lapon a **bejelentkezési oldal védjegyezése** szakaszban kattintson a **Szerkesztés** elemre.
    
    ![Képernyőfelvétel: a bejelentkezési oldal védjegyezési szakasza a Szerkesztés gombbal.](./media/jobscience-tutorial/ic767826.png "Bejelentkezési oldal arculata")

1. A **bejelentkezési oldal védjegyezése** lapon a **hitelesítési szolgáltatás** szakaszban megjelenik az **SAML SSO-beállítások** neve. Jelölje ki, majd kattintson a **Mentés** gombra.
    
    ![Képernyőfelvétel: a bejelentkezési oldal védjegyezési szakasza a PPE-vel és a kijelölt mentése.](./media/jobscience-tutorial/ic784366.png "Bejelentkezési oldal arculata")

1. Az SP által kezdeményezett egyszeri bejelentkezés bejelentkezési URL-címének lekéréséhez kattintson a **biztonsági vezérlők** menü **egyszeri bejelentkezés beállításai** elemére.

    ![A képernyőképen a biztonsági vezérlők felügyeletét láthatja egyetlen Sign-On beállítással.](./media/jobscience-tutorial/ic784368.png "Biztonsági vezérlők")
    
    Kattintson a fenti lépésben létrehozott egyszeri bejelentkezési profilra. Ezen a lapon látható a vállalat egyszeri bejelentkezési URL-címe (például: `https://companyname.my.salesforce.com?so=companyid` ).    

> [!TIP]
> Most már beolvashatja ezeket az utasításokat a [Azure Portalon](https://portal.azure.com)belül, miközben beállíthatja az alkalmazást!  Miután hozzáadta az alkalmazást a **Active Directory > Enterprise Applications** szakaszban, egyszerűen kattintson az **egyszeri bejelentkezés** lapra, és az alján található **konfigurációs** szakaszon keresztül férhet hozzá a beágyazott dokumentációhoz. A beágyazott dokumentációs szolgáltatásról további információt itt talál: az [Azure ad Embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása
Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

![Azure AD-felhasználó létrehozása][100]

**Tesztelési felhasználó Azure AD-beli létrehozásához hajtsa végre a következő lépéseket:**

1. A **Azure Portal** bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![A képernyőképen az Azure A D ikon látható a Azure Portalban.](./media/jobscience-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez lépjen a **felhasználók és csoportok** elemre, és kattintson a **minden felhasználó** lehetőségre.
    
    ![Képernyőfelvétel: a kezelés menü kiválasztott felhasználók és csoportok elemét jeleníti meg, és minden felhasználó ki van választva.](./media/jobscience-tutorial/create_aaduser_02.png) 

1. A **felhasználó** párbeszédpanel megnyitásához kattintson a párbeszédpanel tetején található **Hozzáadás** gombra.
 
    ![Képernyőfelvétel: a Hozzáadás gomb a felhasználó párbeszédpanel megnyitásához.](./media/jobscience-tutorial/create_aaduser_03.png) 

1. A **felhasználó** párbeszédpanelen hajtsa végre a következő lépéseket:
 
    ![Képernyőfelvétel: a felhasználó párbeszédpanel, amelyen megadhatja az ebben a lépésben szereplő értékeket.](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. A **név** szövegmezőbe írja be a következőt: **BrittaSimon**.

    b. A **Felhasználónév** szövegmezőbe írja be a BrittaSimon **e-mail-címét** .

    c. Válassza a **jelszó megjelenítése** lehetőséget, és jegyezze fel a **jelszó** értékét.

    d. Kattintson a **Létrehozás** lehetőségre.
 
### <a name="creating-a-jobscience-test-user"></a>Jobscience-tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Jobscience, a Jobscience-ben kell kiépíteni őket. Jobscience esetén a kiépítés manuális feladat.

>[!NOTE]
>A Jobscience által biztosított egyéb Jobscience-létrehozási eszközöket vagy API-kat használhatja Azure Active Directory felhasználói fiókok kiépítéséhez.
>  
        
**A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Jobscience** vállalati webhelyre rendszergazdaként.

1. Nyissa meg a telepítőt.
   
   ![Képernyőfelvétel: a telepítési elemek megjelenítése.](./media/jobscience-tutorial/ic784358.png "Beállítás")
1. Nyissa meg a **felhasználók kezelése \>** lehetőséget.
   
   ![Felhasználók](./media/jobscience-tutorial/ic784369.png "Felhasználók")
1. Kattintson az **új felhasználó** elemre.
   
   ![Minden felhasználó](./media/jobscience-tutorial/ic784370.png "Minden felhasználó")
1. A **felhasználó szerkesztése** párbeszédpanelen hajtsa végre a következő lépéseket:
   
   ![Felhasználó szerkesztése](./media/jobscience-tutorial/ic784371.png "Felhasználó szerkesztése")
   
   a. Az **Utónév** szövegmezőbe írja be a felhasználó utónevét, például a Britta nevet.
   
   b. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például Simon nevet.
   
   c. Az **alias** szövegmezőbe írja be a felhasználóhoz hasonló alias nevét, például Brittas.

   d. Az **e-mail** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet Brittasimon@contoso.com .

   e. A **Felhasználónév** szövegmezőbe írja be a felhasználó nevét, például: Brittasimon@contoso.com .

   f. A **Nick neve** szövegmezőbe írja be a felhasználó nevét, például Simon.

   : Kattintson a **Mentés** gombra.

    
> [!NOTE]
> A Azure Active Directory fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követve megerősíti a fiókját, mielőtt az aktívvá válna.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Jobscience hozzáférésének biztosításával.

![A képernyőfelvételen a fiók megjelenített neve látható.][200] 

**A Britta Simon Jobscience való hozzárendeléséhez hajtsa végre a következő lépéseket:**

1. A Azure Portal nyissa meg az alkalmazások nézetet, majd lépjen a könyvtár nézetre, és nyissa meg a **vállalati alkalmazások** lehetőséget, majd kattintson a **minden alkalmazás** elemre.

    ![Képernyőfelvétel: a Azure Portal menüben a vállalati alkalmazások a kiválasztott összes alkalmazással láthatók.][201] 

1. Az alkalmazások listában válassza a **Jobscience** lehetőséget.

    ![A képernyőképen a kiválasztott Jobscience látható.](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. A bal oldali menüben kattintson a **felhasználók és csoportok** elemre.

    ![A képernyőképen a Azure Portal menüből kiválasztott felhasználók és csoportok láthatók.][202] 

1. Kattintson a **Hozzáadás** gombra. Ezután válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![Képernyőfelvétel: a Hozzáadás gomb, amely a hozzárendelések hozzáadására szolgál.][203]

1. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán.

1. Kattintson a **felhasználók és csoportok** párbeszédpanel **kijelölés** gombjára.

1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Jobscience csempére kattint, a rendszer automatikusan bejelentkezett a Jobscience alkalmazásba.
További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

* [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png