---
title: 'Oktatóanyag: Azure Active Directory integráció a Mitel-csatlakozással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Mitel közötti kapcsolat között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/31/2020
ms.author: jeedes
ms.openlocfilehash: b89ae74a1f4ead568a5454c9494d4a82b0eeea3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015179"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect-or-cloudlink-platform"></a>Oktatóanyag: Azure Active Directory integráció a Mitel MiCloud csatlakozással vagy a CloudLink platformmal

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Mitel-összekötő alkalmazást a Azure Active Directory (Azure AD) integrálásához a Mitel MiCloud vagy a CloudLink platformmal. Az Mitel-összekötő alkalmazás az Azure Galleryben érhető el. Az Azure AD és a MiCloud-kapcsolat, illetve a CloudLink platform integrálása a következő előnyöket biztosítja:

* A felhasználók hozzáférését a MiCloud és az Azure AD-ben a vállalati hitelesítő adataik használatával is CloudLink.
* A fiókjában lévő felhasználók számára engedélyezheti, hogy a rendszer automatikusan bejelentkezzen a MiCloud csatlakozni vagy a CloudLink (egyszeri bejelentkezés) az Azure AD-fiókjával.

Az Azure AD-vel való SaaS-alkalmazások integrálásával kapcsolatos részletekért lásd: [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) , mielőtt megkezdené az Azure ad-integrációt az Mitel MiCloud csatlakozás vagy a CloudLink platformmal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció MiCloud-kapcsolaton keresztüli konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A konfigurálni kívánt alkalmazástól függően egy Mitel MiCloud-fiók vagy Mitel CloudLink-fiók.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezést (SSO) konfigurálja és teszteli.

* Az Mitel-összekötő támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A Mitel-kapcsolat konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós idejű védelmet biztosít a szervezet bizalmas adatai kiszűrése és beszivárgásának. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mitel-connect-from-the-gallery"></a>Mitel-kapcsolat hozzáadása a katalógusból

A Mitel az Azure AD-be való csatlakozásának konfigurálásához hozzá kell adnia a Mitel csatlakozást a katalógusból a felügyelt SaaS-alkalmazások listájához a Azure Portal.

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Válassza a **vállalati alkalmazások** lehetőséget, majd válassza **a minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Válassza az **Új alkalmazás** lehetőséget.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. Írja be a **Mitel-kapcsolat** kifejezést a Keresés mezőbe, válassza a **Mitel-kapcsolat** az eredmények panelen lehetőséget, majd válassza a **Hozzáadás** lehetőséget.

     ![Mitel-kapcsolat az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD SSO konfigurálását és tesztelését MiCloud-kapcsolattal vagy CloudLink-platformmal végezheti el egy **_Britta Simon_** nevű teszt felhasználó alapján. Az egyszeri bejelentkezés működéséhez a felhasználónak az Azure AD Portálon és a Mitel platformon a megfelelő felhasználón keresztül kell létrehoznia a kapcsolatot. Tekintse át az alábbi, az Azure AD SSO konfigurálásával és tesztelésével kapcsolatos tudnivalókat a MiCloud-kapcsolat vagy a CloudLink platform használatával.
* Azure AD SSO konfigurálása és tesztelése MiCloud-kapcsolattal
* Azure AD SSO konfigurálása és tesztelése CloudLink platformmal

## <a name="configure-and-test-azure-ad-sso-with-micloud-connect"></a>Azure AD SSO konfigurálása és tesztelése MiCloud-kapcsolattal

Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az MiCloud-kapcsolattal:

1. **[Konfigurálja az MiCloud-hez való kapcsolódást az egyszeri bejelentkezéshez az Azure ad-vel](#configure-micloud-connect-for-sso-with-azure-ad)** – lehetővé teszi, hogy a felhasználók ezt a funkciót használják, és az egyszeri bejelentkezés beállításait az alkalmazás oldalán konfigurálja.
2. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
3. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
4. **[Hozzon létre egy Mitel MiCloud kapcsolódási teszt felhasználóval](#create-a-mitel-micloud-connect-test-user)** , hogy a felhasználó Azure ad-képviseletéhez kapcsolódó MiCloud-csatlakozási fiókhoz tartozó Britta Simon partnere legyen.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>MiCloud-kapcsolat konfigurálása egyszeri bejelentkezéshez az Azure AD-vel

Ebben a szakaszban engedélyezni fogja az Azure AD egyszeri bejelentkezést a MiCloud kapcsolódáshoz a Azure Portalban, és konfigurálja a MiCloud csatlakozási fiókját az egyszeri bejelentkezés engedélyezéséhez az Azure AD használatával.

A MiCloud az Azure AD-vel való összekapcsolásának konfigurálásához a legegyszerűbben a Azure Portal és a Mitel-fiók portálját kell megnyitnia egymás mellett. Néhány információt át kell másolnia a Azure Portalról a Mitel-fiók portálra, és néhányat a Mitel-fiók portálról a Azure Portalra.


1. A [Azure Portal](https://portal.azure.com/)konfiguráció oldalának megnyitása:

    1. A **Mitel csatlakozás** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

       ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

    1. Az **egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza az **SAML** lehetőséget.
    
       ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)
    
       Megjelenik az SAML-alapú bejelentkezési oldal.

2. A Mitel-fiók portálon a konfiguráció párbeszédpanel megnyitásához:

    1. A **telefonos rendszer** menüben válassza a **kiegészítő funkciók** lehetőséget.

    1. Az **egyszeri bejelentkezéstől** jobbra válassza az **aktiválás** vagy a **Beállítások** lehetőséget.
    
    Megjelenik az önálló Sign-On-beállítások összekötése párbeszédpanel.
    
3. Jelölje be az **egyszeri bejelentkezés engedélyezése** jelölőnégyzetet.
    
    ![Képernyőfelvétel: az egyetlen Sign-On engedélyezése jelölőnégyzet bejelölésével megjelenítheti a Mitel-összekapcsolási Sign-On beállításait tartalmazó lapot.](./media/mitel-connect-tutorial/mitel-connect-enable.png)

4. A Azure Portal válassza a **Szerkesztés** ikont az **alapszintű SAML-konfiguráció** szakaszban.
   
    ![Képernyőfelvétel: az egyszeri Sign-On beállítása az SAML-oldallal lapon a Szerkesztés ikon kiválasztva.](common/edit-urls.png)

    Megjelenik az alapszintű SAML-konfiguráció párbeszédpanel.

5.  Másolja az URL-címet a Mitel-fiók portál Mitel-azonosító **(Entity ID)** mezőjéből, és illessze be a Azure Portal **azonosító (entitás azonosítója)** mezőjébe.

6. Másolja az URL-címet a válasz URL-címéből **(az állítási fogyasztói szolgáltatás URL-címe)** a Mitel-fiók portálon, és illessze be a **Válasz URL-címébe (a felhasználói szolgáltatás URL-címe)** mezőben a Azure Portal.

   ![Képernyőfelvétel: alapszintű SAML-konfiguráció a Azure Portal és az Identitáskezelő beállítása szakasz a Mitel-fiók portálon a köztük lévő kapcsolatot jelző vonalakkal.](./media/mitel-connect-tutorial/mitel-azure-basic-configuration.png)

7. A **bejelentkezési URL-cím** szövegmezőbe írja be az alábbi URL-címek egyikét:

    1. **https://portal.shoretelsky.com** – a Mitel-fiók portál használata alapértelmezett Mitel-alkalmazásként
    1. **https://teamwork.shoretel.com** – a csapatmunka alapértelmezett Mitel-alkalmazásként való használata

    > [!NOTE]
    > Az alapértelmezett Mitel alkalmazás az az alkalmazás, amely akkor érhető el, amikor egy felhasználó kiválasztja a Mitel csatlakozás csempét a hozzáférési panelen. Ez az alkalmazás akkor is elérhető, amikor az Azure AD-ból tesztet telepít.

8. A Azure Portal **alapszintű SAML-konfiguráció** párbeszédpanelén válassza a **Mentés** lehetőséget.

9. A Azure Portal **SAML-alapú bejelentkezési** lapján az **SAML aláíró tanúsítvány** szakaszban válassza a **tanúsítvány (Base64)** melletti **Letöltés** lehetőséget az **aláíró tanúsítvány** letöltéséhez és a számítógépbe való mentéséhez.

    ![Képernyőfelvétel: az SAML aláíró tanúsítvány panel, ahol letöltheti a tanúsítványokat.](./media/mitel-connect-tutorial/azure-signing-certificate.png)

10. Nyissa meg az aláíró tanúsítványfájl egy szövegszerkesztőben, másolja a fájlban található összes adatfájlt, majd illessze be az adattípust a Mitel-fiók portál **aláíró tanúsítvány** mezőjébe. 

      ![A képernyőképen az aláíró tanúsítvány mező látható.](./media/mitel-connect-tutorial/mitel-connect-signing-certificate.png)

11. A Azure Portal **SAML-alapú bejelentkezési** oldalán, a **Mitel-kapcsolat beállítása** szakaszban:

     1. Másolja az URL-címet a **bejelentkezési URL** -cím mezőbe, és illessze be a **bejelentkezési URL** mezőbe a Mitel-fiók portálon.

     1. Másolja az URL-címet az **Azure ad-azonosító** mezőjéből, és illessze be a Mitel-fiók-portál **entitás-azonosító** mezőjébe.
         
         ![Képernyőfelvétel: a Azure Portal és a Mitel-fiók portál SAML-alapú bejelentkezési lapja közötti kapcsolat.](./media/mitel-connect-tutorial/mitel-azure-set-up-connect.png)

12. Válassza a **Mentés** lehetőséget a Mitel-fiók portálon az **egyszeres Sign-On beállítások összekapcsolása** párbeszédpanelen.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ebben a szakaszban egy Britta Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai párbeszédpanelen hajtsa végre a következő lépéseket:

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    1. A **név** mezőbe írja be a következőt: **BrittaSimon**.
  
    1. A **Felhasználónév** mezőbe írja be a következőt: brittasimon@ \<yourcompanydomain\> . \<extension\> .  Például: BrittaSimon@contoso.com.

    1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.

    1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Mitel kapcsolódáshoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Mitel-kapcsolat** lehetőséget.

    ![Az Mitel kapcsolódási hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a **felhasználók** listában, majd a képernyő alján válassza a **kiválasztás** lehetőséget.

6. Ha az SAML-kijelentésben bármelyik szerepkör értékét várja, válassza ki a megfelelő szerepkört a felhasználó számára a **szerepkör kiválasztása** párbeszédpanelen, majd válassza a **kiválasztás** lehetőséget a képernyő alján.

7. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Mitel-MiCloud csatlakozási teszt felhasználó létrehozása

Ebben a szakaszban létrehoz egy Britta Simon nevű felhasználót a MiCloud-összekötő fiókjában. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

A felhasználók a Mitel-fiók portálon való hozzáadásával kapcsolatos részletekért tekintse meg a [felhasználói cikk hozzáadása](https://shoretelcommunity.force.com/s/article/Adding-Users-092815) a Mitel Tudásbázisban című témakört.

Hozzon létre egy felhasználót a MiCloud csatlakozási fiókjában a következő részletekkel:

* **Név:** Britta Simon
* **Üzleti e-mail-cím:**`brittasimon@<yourcompanydomain>.<extension>`   
  (Példa: [brittasimon@contoso.com](mailto:brittasimon@contoso.com) )
* **Felhasználónév:**`brittasimon@<yourcompanydomain>.<extension>`  
  (Példa: [brittasimon@contoso.com](mailto:brittasimon@contoso.com) ; a felhasználó felhasználóneve általában ugyanaz, mint a felhasználó üzleti e-mail-címe.

> [!NOTE]
> A felhasználó MiCloud-kapcsolati felhasználónevének meg kell egyeznie a felhasználó e-mail-címével az Azure-ban.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával tesztelheti.

Amikor kiválasztja a Mitel csatlakozás csempét a hozzáférési panelen, a rendszer automatikusan átirányítja a bejelentkezési **URL-cím** mezőben az alapértelmezettként konfigurált MiCloud-csatlakozási alkalmazásba való bejelentkezéshez. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="configure-and-test-azure-ad-sso-with-cloudlink-platform"></a>Azure AD SSO konfigurálása és tesztelése CloudLink platformmal

Ez a szakasz azt ismerteti, hogyan engedélyezhető az Azure AD SSO a CloudLink platformhoz a Azure Portalban, és hogyan konfigurálható a CloudLink-platform fiókja az egyszeri bejelentkezés engedélyezéséhez az Azure AD használatával.

Ha a CloudLink platformot egyszeri bejelentkezéssel szeretné konfigurálni az Azure AD-hez, javasoljuk, hogy nyissa meg a Azure Portal és a CloudLink-fiókok portált egymás mellett, mivel a Azure Portal adatait át kell másolnia a CloudLink-fiókok portálra, és fordítva.

1. A [Azure Portal](https://portal.azure.com/)konfiguráció oldalának megnyitása:

    1. A **Mitel csatlakozás** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

       ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

    1. Az **egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza az **SAML** lehetőséget.

       ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)
    
       Megnyílik az **SAML-alapú bejelentkezési** oldal, amely megjeleníti az **alapszintű SAML-konfiguráció** szakaszát.

       ![Képernyőfelvétel: az SAML-alapú bejelentkezési oldal, amely alapszintű SAML-konfigurációt mutat be.](./media/mitel-connect-tutorial/mitel-azure-saml-settings.png)

2. Az **Azure ad egyszeri bejelentkezés** konfigurációs paneljének eléréséhez a CloudLink-fiókok portálján:

    1. Lépjen annak az ügyfél-fióknak a **fiók adatai** lapjára, amelyen engedélyezni szeretné az integrációt.

    1. Az **integrációk** szakaszban válassza az **+ új hozzáadása** elemet. Az előugró képernyő az **integrációk** panelt jeleníti meg.

    1. Válassza ki a **harmadik féltől származó** lapot. Megjelenik a támogatott harmadik féltől származó alkalmazások listája. Válassza az **Azure ad egyszeri bejelentkezéshez** társított **Hozzáadás** gombot, és válassza a **kész** lehetőséget.

       ![Képernyőfelvétel: az integrációs oldal, amelyen hozzáadhatja az Azure D egyszeri bejelentkezést.](./media/mitel-connect-tutorial/mitel-cloudlink-integrations.png)

       Az **Azure ad egyszeri bejelentkezés** engedélyezve van az ügyfél fiókjához, és a rendszer hozzáadja a **fiók adatai** lap **integrációk** szakaszához.   

   1. Válassza a **telepítés befejezése** lehetőséget.
    
      ![A képernyőképen az Azure A D egyszeri bejelentkezéshez használható teljes telepítési lehetőség látható.](./media/mitel-connect-tutorial/mitel-cloudlink-complete-setup.png)
      
      Megnyílik az **Azure ad egyszeri bejelentkezés** konfigurációs panelje.
      
       ![A képernyőfelvételen az Azure A D-Sign-On konfigurációja látható.](./media/mitel-connect-tutorial/mitel-cloudlink-sso-setup.png)
       
       A Mitel azt javasolja, hogy a **Mitel hitelesítő adatok engedélyezése (nem kötelező)** jelölőnégyzet ne legyen bejelölve a **választható Mitel hitelesítő adatok** szakaszban. Jelölje be ezt a jelölőnégyzetet, ha azt szeretné, hogy a felhasználó a Mitel hitelesítő adataival jelentkezzen be a CloudLink alkalmazásba az egyszeri bejelentkezés lehetőség mellett.

3. A Azure Portal az **SAML-alapú bejelentkezési** oldalon válassza a **Szerkesztés** ikont az **alapszintű SAML-konfiguráció** szakaszban. Megnyílik az **alapszintű SAML-konfiguráció** panel.

    ![A képernyőképen az alapszintű SAML-konfiguráció ablaktábla látható, ahol a Szerkesztés ikon van kiválasztva.](./media/mitel-connect-tutorial/mitel-azure-saml-basic.png)
 
 4. Másolja az URL-címet a CloudLink-fiókok portál Mitel-azonosító **(Entity ID)** mezőjéből, és illessze be a Azure Portal **azonosító (entitás azonosítója)** mezőjébe.

 5. Másolja az URL-címet a CloudLink-fiókok portál **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** mezőből, és illessze be a **Válasz URL-címébe (a felhasználói szolgáltatás URL-címe)** mezőben a Azure Portal.  
    
    ![A képernyőképen látható a CloudLink-fiókok és a Azure Portal lapjai közötti kapcsolat.](./media/mitel-connect-tutorial/mitel-cloudlink-saml-mapping.png) 

 6. A **bejelentkezési URL** szövegmezőbe írja be azt az URL-címet, `https://accounts.mitel.io` amely a CloudLink-fiókok portált használja alapértelmezett Mitel-alkalmazásként.
     
     ![A képernyőképen a bejelentkezés U R L szövegmező jelenik meg.](./media/mitel-connect-tutorial/mitel-cloudlink-sign-on-url.png)
  
     > [!NOTE]
     > Az alapértelmezett Mitel alkalmazás az az alkalmazás, amely akkor nyílik meg, amikor a felhasználó kiválasztja a Mitel csatlakozás csempét a hozzáférési panelen. Ez az alkalmazás akkor is elérhető, amikor a felhasználó az Azure AD-ből konfigurál egy teszt-beállítást.

7. Válassza a **Mentés** lehetőséget az **ALAPszintű SAML-konfiguráció** párbeszédpanelen.

8. Az Azure Portal **SAML-alapú bejelentkezési** lapján az **SAML aláíró tanúsítvány** szakaszban a **tanúsítvány (Base64)** melletti **Letöltés** lehetőségre kattintva töltse le az **aláíró tanúsítványt**. Mentse a tanúsítványt a számítógépére.
  
    ![A képernyőfelvételen az SAML aláíró tanúsítvány szakasza látható, ahol letöltheti a Base64-tanúsítványt.](./media/mitel-connect-tutorial/mitel-cloudlink-save-certificate.png)

9. Nyissa meg az aláíró tanúsítványfájl egy szövegszerkesztőben, másolja a fájlban található összes adatfájlt, majd illessze be az adatbevitelt a CloudLink-fiókok portál **aláíró tanúsítvány** mezőjébe.  

    > [!NOTE]
    > Ha egynél több tanúsítványa van, azt javasoljuk, hogy illessze be őket egymás után. 
       
    ![A képernyőképen az eljárás második lépése látható, amely az Azure A D-integrációban található értékeket tölti ki.](./media/mitel-connect-tutorial/mitel-cloudlink-enter-certificate.png)

10. A Azure Portal **SAML-alapú bejelentkezési** lapján a **Mitel-kapcsolat beállítása** szakaszban:

     1. Másolja az URL-címet a **bejelentkezési URL** -cím mezőbe, és illessze be azt a CloudLink-fiókok portál **bejelentkezési URL** mezőjébe.

     1. Másolja az URL-címet az **Azure ad azonosító** mezőjéből, és illessze be a CloudLink-fiókok portál **identitásszolgáltató-azonosító (Entity ID)** mezőjébe.
     
        ![A képernyőképen az itt ismertetett értékek forrása látható a Mintel-kapcsolatban.](./media/mitel-connect-tutorial/mitel-cloudlink-copy-settings.png)

11. Válassza a **Mentés** lehetőséget az **Azure ad egyszeri bejelentkezés** paneljén a CloudLink-fiókok portálján.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ebben a szakaszban egy Britta Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai párbeszédpanelen hajtsa végre a következő lépéseket:

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    1. A **név** mezőbe írja be a következőt: **BrittaSimon**.
  
    1. A **Felhasználónév** mezőbe írja be a következőt: brittasimon@ \<yourcompanydomain\> . \<extension\> .  Például: BrittaSimon@contoso.com.

    1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.

    1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Mitel kapcsolódáshoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Mitel-kapcsolat** lehetőséget.

    ![Az Mitel kapcsolódási hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a **felhasználók** listában, majd a képernyő alján válassza a **kiválasztás** lehetőséget.

6. Ha az SAML-kijelentésben bármelyik szerepkör értékét várja, válassza ki a megfelelő szerepkört a felhasználó számára a **szerepkör kiválasztása** párbeszédpanelen, majd válassza a **kiválasztás** lehetőséget a képernyő alján.

7. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

### <a name="create-a-cloudlink-test-user"></a>CloudLink-teszt felhasználó létrehozása

Ez a szakasz azt ismerteti, hogyan lehet létrehozni egy **_Britta Simon_** nevű teszt felhasználót a CloudLink platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

A felhasználók a CloudLink-fiókok portálján való hozzáadásával kapcsolatos részletekért lásd: **_felhasználók kezelése_** a [CloudLink-fiókok dokumentációjában](https://www.mitel.com/document-center/technology/cloudlink/all-releases/en/cloudlink-accounts-html).

Hozzon létre egy felhasználót a CloudLink-fiókok portálján a következő részletekkel:

* Név: Britta Simon
* Keresztnév: Britta
* Vezetéknév: Simon
* E-mail: BrittaSimon@contoso.com

> [!NOTE]
> A felhasználó CloudLink e-mail-címének meg kell egyeznie az **egyszerű felhasználónévvel** a Azure Portalban.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD SSO konfigurációját a hozzáférési panel használatával tesztelheti.

Amikor kiválasztja a Mitel csatlakozás csempét a hozzáférési panelen, a rendszer automatikusan átirányítja a bejelentkezési **URL-cím** mezőben az alapértelmezettként konfigurált CloudLink-alkalmazásba való bejelentkezéshez. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)