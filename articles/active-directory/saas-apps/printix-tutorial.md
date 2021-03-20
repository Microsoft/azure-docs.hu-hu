---
title: 'Oktatóanyag: Azure Active Directory integráció a Printix-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Printix között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: dfde9bbbeb7f6b349ecbdc4c2da605d39a0708da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357878"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Oktatóanyag: Azure Active Directory integráció a Printix

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Printix a Azure Active Directory (Azure AD) szolgáltatással.

A Printix és az Azure AD integrálásával a következő előnyöket nyújtja:

- Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Printix
- Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkeznek a Printix (egyszeri bejelentkezés) az Azure AD-fiókkal
- A fiókokat egyetlen központi helyen kezelheti – a Azure Portal

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Printix való konfigurálásához a következő elemek szükségesek:

- Azure AD-előfizetés
- Printix egyszeri bejelentkezésre engedélyezett előfizetés

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem javasoljuk éles környezet használatát.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi javaslatokat:

- Ne használja éles környezetét, ha szükséges.
- Ha még nem rendelkezik Azure AD-próbaverzióval, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos próbaverziót.

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését teszteli a tesztelési környezetben. Az oktatóanyagban ismertetett forgatókönyv két fő építőelemet tartalmaz:

1. Printix hozzáadása a gyűjteményből
1. Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

## <a name="adding-printix-from-the-gallery"></a>Printix hozzáadása a gyűjteményből
A Printix Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Printix a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Printix szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazásokhoz**. Ezután nyissa meg az **összes alkalmazást**.

    ![A képernyőképen a felügyelet alatt kiválasztott Azure Portal vállalati alkalmazások láthatók, és minden alkalmazás ki van választva.][2]
    
1. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![A képernyőképen a kiválasztott új alkalmazás látható.][3]

1. A keresőmezőbe írja be a **Printix** kifejezést.

    ![A képernyőképen a Hozzáadás a katalógusból párbeszédpanelen megjelenő Printix keresése látható.](./media/printix-tutorial/tutorial_printix_search.png)

1. Az eredmények panelen válassza a **Printix** lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![A képernyőképen a kiválasztott Printix beállítás látható.](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése
Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Printix-mel konfigurálja és teszteli a "Britta Simon" nevű tesztelési felhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD-nek tudnia kell, hogy mi a Printix felhasználója az Azure AD-ben. Más szóval az Azure AD-felhasználó és a Printix kapcsolódó felhasználó közötti kapcsolati kapcsolat létesítésére van szükség.

A Printix-ben rendelje hozzá a **Felhasználónév értékét** az Azure ad-ben a kapcsolati kapcsolat létrehozásához használt **Felhasználónév** értékeként.

Az Azure AD egyszeri bejelentkezés Printix való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
1. **[Azure ad-felhasználó létrehozása](#creating-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
1. **[Printix-teszt felhasználó létrehozása](#creating-a-printix-test-user)** – ahhoz, hogy a Printix Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
1. **[Az Azure ad-teszt felhasználójának kiosztása](#assigning-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portalban, és konfigurálhatja az egyszeri bejelentkezést a Printix alkalmazásban.

**Az Azure AD egyszeri bejelentkezés Printix való konfigurálásához hajtsa végre a következő lépéseket:**

1. A Azure Portal **Printix** alkalmazás-integráció lapján kattintson az **egyszeri bejelentkezés** elemre.

    ![Képernyőfelvétel: az Azure Portal a kezelés elemnél kiválasztott egyszeri bejelentkezést jeleníti meg.][4]

1. Az **egyszeri** Bejelentkezés párbeszédpanelen válassza a **mód** **SAML-alapú bejelentkezés** lehetőséget az egyszeri bejelentkezés engedélyezéséhez.
 
    ![A képernyőfelvételen az SAML-alapú bejelentkezési mód van kiválasztva.](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. A **Printix tartomány és URL-címek** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a Printix tartomány és URL-címek szakasz, ahol megadhatja az U R L-t a bejelentkezéshez.](./media/printix-tutorial/tutorial_printix_url.png)

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.printix.net`

    > [!NOTE] 
    > Az érték nem valódi. Frissítse az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez forduljon a Printix ügyfélszolgálati [csapatához](mailto:support@printix.net) . 
 
1. Az **SAML aláíró tanúsítvány** szakaszban kattintson a **metaadatok XML** elemre, majd mentse a metaadat-fájlt a számítógépen.

    ![Képernyőfelvétel: az SAML aláíró tanúsítvány panel, ahol letöltheti a tanúsítványokat.](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. Kattintson a **Save (Mentés** ) gombra.

    ![A Mentés gomb képernyőképe.](./media/printix-tutorial/tutorial_general_400.png)

1. Jelentkezzen be rendszergazdaként a Printix-bérlőbe.

1. A felső menüben kattintson a jobb felső sarokban lévő ikonra, és válassza a "**hitelesítés**" lehetőséget.
   
    ![A képernyőképen a menüből kiválasztott hitelesítés látható.](./media/printix-tutorial/tutorial_printix_06.png)

1. A **telepítés** lapon válassza az **Azure/Office 365-hitelesítés engedélyezése** lehetőséget.
   
    ![Képernyőfelvétel: a Printix.net lap, amelyen kiválaszthatja az Azure/Office 365-hitelesítés engedélyezése lehetőséget.](./media/printix-tutorial/tutorial_printix_07.png)

1. Az **Azure** lapon adja meg a "**összevonás metaadatainak dokumentum**" szövegmezőbe az összevonási metaadatok URL-címét. 

    Csatolja a metaadatokat tartalmazó XML-fájlt, amelyet az Azure AD-ből töltött le az [Printix támogatási csapatának](mailto:support@printix.net). Ezután feltöltik az XML-fájlt, és megadják az összevonási metaadatok URL-címét.
   
    ![Képernyőfelvétel: a Printix.net oldal, amelyen megadhatja az összevonási metaadatokat tartalmazó dokumentumot.](./media/printix-tutorial/tutorial_printix_08.png)
   
1. Kattintson a "**teszt**" gombra, és kattintson az "**OK**" gombra, ha a teszt sikeres volt.
   
     Az Azure Active Directory oldal a **tesztelés** gombra kattintva jelenik meg. "A teszt sikeres volt": az Azure-beli tesztelési fiók hitelesítő adatainak megadása után megjelenik egy üzenet, amely "a beállítások tesztelték az OK" üzenetet. Ezután kattintson az **OK** gombra.
   
    ![A teszt eredményei a képernyőképen láthatók.](./media/printix-tutorial/tutorial_printix_09.png)

1. Kattintson a **Mentés** gombra a "**hitelesítés**" lapon.


> [!TIP]
> Most már beolvashatja ezeket az utasításokat a [Azure Portalon](https://portal.azure.com)belül, miközben beállíthatja az alkalmazást!  Miután hozzáadta az alkalmazást a **Active Directory > Enterprise Applications** szakaszban, egyszerűen kattintson az **egyszeri bejelentkezés** lapra, és az alján található **konfigurációs** szakaszon keresztül férhet hozzá a beágyazott dokumentációhoz. A beágyazott dokumentációs szolgáltatásról további információt itt talál: az [Azure ad Embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása
Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

![Azure AD-felhasználó létrehozása][100]

**Tesztelési felhasználó Azure AD-beli létrehozásához hajtsa végre a következő lépéseket:**

1. A **Azure Portal** bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![A képernyőkép a létrehozandó nevet és felhasználónevet jeleníti meg.](./media/printix-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez lépjen a **felhasználók és csoportok** elemre, és kattintson a **minden felhasználó** lehetőségre.
    
    ![A képernyőképen az Azure A D ikon látható a Azure Portalban.](./media/printix-tutorial/create_aaduser_02.png) 

1. A **felhasználó** párbeszédpanel megnyitásához kattintson a párbeszédpanel tetején található **Hozzáadás** gombra.
 
    ![Képernyőfelvétel: a kezelés menü kiválasztott felhasználók és csoportok elemét jeleníti meg, és minden felhasználó ki van választva.](./media/printix-tutorial/create_aaduser_03.png) 

1. A **felhasználó** párbeszédpanelen hajtsa végre a következő lépéseket:
 
    ![Képernyőfelvétel: a felhasználó párbeszédpanel, amelyen megadhatja a leírt értékeket.](./media/printix-tutorial/create_aaduser_04.png) 

    a. A **név** szövegmezőbe írja be a következőt: **BrittaSimon**.

    b. A **Felhasználónév** szövegmezőbe írja be a BrittaSimon **e-mail-címét** .

    c. Válassza a **jelszó megjelenítése** lehetőséget, és jegyezze fel a **jelszó** értékét.

    d. Kattintson a **Létrehozás** lehetőségre.
 
### <a name="creating-a-printix-test-user"></a>Printix-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a Printix-ben. A Printix támogatja az igény szerinti üzembe helyezést, amely alapértelmezés szerint engedélyezve van.

Ez a szakasz nem tartalmaz műveleti elemeket. Új felhasználó jön létre a Printix elérésére tett kísérlet során, ha még nem létezik. 

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [Printix támogatási csapatával](mailto:support@printix.net).
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Printix hozzáférésének biztosításával.

![A képernyőfelvétel egy alapértelmezett hozzáféréssel rendelkező felhasználót mutat be.][200] 

**A Britta Simon Printix való hozzárendeléséhez hajtsa végre a következő lépéseket:**

1. A Azure Portal nyissa meg az alkalmazások nézetet, majd lépjen a könyvtár nézetre, és nyissa meg a **vállalati alkalmazások** lehetőséget, majd kattintson a **minden alkalmazás** elemre.

    ![A képernyőképen a kezelés alatt kiválasztott vállalati alkalmazások láthatók, és minden alkalmazás ki van választva.][201] 

1. Az alkalmazások listában válassza a **Printix** lehetőséget.

    ![A képernyőképen az alkalmazások listája látható, ahol kiválaszthatja a Printix.](./media/printix-tutorial/tutorial_printix_app.png) 

1. A bal oldali menüben kattintson a **felhasználók és csoportok** elemre.

    ![A képernyőfelvételen a kezelés menüből kiválasztott felhasználók és csoportok láthatók.][202] 

1. Kattintson a **Hozzáadás** gombra. Ezután válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![Képernyőfelvétel: a Hozzáadás gomb és a hozzárendelés hozzáadása lap, ahol kiválaszthatja a felhasználókat és a csoportokat.][203]

1. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán.

1. Kattintson a **felhasználók és csoportok** párbeszédpanel **kijelölés** gombjára.

1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Printix csempére kattint, a rendszer automatikusan bejelentkezett a Printix alkalmazásba.

## <a name="additional-resources"></a>További források

* [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/printix-tutorial/tutorial_general_01.png
[2]: ./media/printix-tutorial/tutorial_general_02.png
[3]: ./media/printix-tutorial/tutorial_general_03.png
[4]: ./media/printix-tutorial/tutorial_general_04.png

[100]: ./media/printix-tutorial/tutorial_general_100.png

[200]: ./media/printix-tutorial/tutorial_general_200.png
[201]: ./media/printix-tutorial/tutorial_general_201.png
[202]: ./media/printix-tutorial/tutorial_general_202.png
[203]: ./media/printix-tutorial/tutorial_general_203.png

