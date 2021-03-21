---
title: 'Oktatóanyag: Azure Active Directory integráció az E Sales Managerrel – Remix | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és E Sales Manager remixek között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: c06595b683092abf52300481068daab26394c4cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995641"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Azure Active Directory integrálása az E Sales Manager remixekkel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja Azure Active Directory (Azure AD) az E Sales Manager remixekkel.

Az Azure AD és az E Sales Manager Remix integrálásával a következő előnyöket kapja:

- Az Azure AD-ben beállíthatja, hogy ki férhet hozzá az E Sales Manager Remixekhez.
- Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az E Sales Manager Remixbe (egyszeri bejelentkezés vagy SSO) az Azure AD-fiókkal.
- A fiókokat egy központi helyen, a Azure Portal is kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az E Sales Manager remixekkel való konfigurálásához a következő elemek szükségesek:

- Azure AD-előfizetés
- E Sales Manager Remix SSO-kompatibilis előfizetés

> [!NOTE]
> Ha teszteli az oktatóanyag lépéseit, javasoljuk, hogy *ne használjon éles* környezetet.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi javaslatokat:

- Ne használja éles környezetét, ha szükséges.
- Ha nem rendelkezik Azure AD-próbaverzióval, egy [hónapos próbaverziót is beszerezhet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését teszteli a tesztelési környezetben. 

Az oktatóanyagban ismertetett forgatókönyv két fő építőelemet tartalmaz:

* E Sales Manager Remix hozzáadása a katalógusból
* Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>E Sales Manager Remix hozzáadása a katalógusból
Az Azure AD és az E Sales Manager Remix integrálásának konfigurálásához adja hozzá az E Sales Manager remixeket a katalógusból a felügyelt SaaS-alkalmazások listájához a következő módon:

1. A [Azure Portal](https://portal.azure.com)a bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget. 

    ![A Azure Active Directory gomb][1]

1. Válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.

    ![A "vállalati alkalmazások" ablak][2]
    
1. Új alkalmazás hozzáadásához válassza az ablak tetején található **új alkalmazás** lehetőséget.

    ![Az új alkalmazás gomb][3]

1. A keresőmezőbe írja be a következőt: **e Sales Manager Remix**, válassza az **e Sales Manager Remix** elemet az eredmények listájában, majd kattintson a **Hozzáadás** gombra.

    ![E Sales Manager Remix az eredmények listájában](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az E Sales Manager remixekkel konfigurálja és teszteli a "Britta Simon" nevű tesztelési felhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD-nek azonosítania kell az E Sales Manager Remix felhasználót és annak megfelelőjét az Azure AD-ben. Más szóval az Azure AD-felhasználók és az E Sales Manager-remixek közötti kapcsolati kapcsolat létrehozása szükséges.

Az Azure AD egyszeri bejelentkezés az E Sales Manager remixekkel való konfigurálásához és teszteléséhez hajtsa végre az építőelemeket a következő öt szakaszban:

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Engedélyezze az Azure AD egyszeri bejelentkezést a Azure Portalban, és konfigurálja az egyszeri bejelentkezést az E Sales Manager Remix alkalmazásban az alábbi módon:

1. Az Azure Portal az **E Sales Manager Remix** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Az "egyszeri bejelentkezés" hivatkozás][4]

1. Az **egyszeri bejelentkezési** ablakban az **egyszeri bejelentkezési mód** mezőben válassza az **SAML-alapú bejelentkezés** lehetőséget.
 
    ![Az "egyszeri bejelentkezés" ablak](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. Az **E Sales Manager Remix tartomány és URL-címek** területen tegye a következőket:

    ![E Sales Manager Remix tartomány és URL-címek egyszeri bejelentkezési adatai](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. A **bejelentkezési URL-cím** mezőbe írja be az URL-címet a következő formátumban: *https:// \<Server-Based-URL> / \<sub-domain> /esales-PC*.

    b. Az **azonosító** mezőbe írja be az URL-címet a következő formátumban: *https:// \<Server-Based-URL> / \<sub-domain> /*.

    c. Jegyezze fel az **azonosító** értékét az oktatóanyag későbbi részében való használatra.
    
    > [!NOTE] 
    > Az előző értékek nem valósak. Frissítse őket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek beszerzéséhez vegye fel a kapcsolatot az [E Sales Manager Remix ügyfél-támogatási csapatával](mailto:esupport@softbrain.co.jp).

1. Az **SAML aláíró tanúsítvány** területen válassza a **tanúsítvány (Base64)** lehetőséget, majd mentse a tanúsítványt a számítógépre.

    ![A tanúsítvány (Base64) letöltési hivatkozása](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Jelölje be az **összes többi felhasználói attribútum megtekintése és szerkesztése** jelölőnégyzetet, majd válassza ki az **EmailAddress** attribútumot.
    
    ![A felhasználói attribútumok ablak](./media/esalesmanagerremix-tutorial/configure1.png)

    Megnyílik az **attribútum szerkesztése** ablak.

1. Másolja a **névtér** és a **név** értékét. Állítson elő értéket a mintában *\<Namespace>/\<Name>* , és mentse az oktatóanyag későbbi részében való használatra.

    ![Az attribútum szerkesztése ablak](./media/esalesmanagerremix-tutorial/configure2.png)

1. Az **e Sales Manager Remix-konfiguráció** területén válassza az **e Sales Manager-remixek konfigurálása** lehetőséget.

    ![Képernyőfelvétel: az "E Sales Manager Remix Configuration" szakasz "az E Sales Manager remixek konfigurálása" című részében látható.](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    Megnyílik a **bejelentkezés beállítása** ablak.

1. A **rövid útmutató** szakaszban másolja ki a kijelentkezési URL-címet és az SAML egyszeri bejelentkezési szolgáltatás URL-címét.

1. Kattintson a **Mentés** gombra.

    ![A Mentés gomb](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Jelentkezzen be az E Sales Manager Remix-alkalmazásba rendszergazdaként.

1. A jobb felső sarokban válassza **a rendszergazda menüt**.

    ![A "felügyeleti menü" parancs](./media/esalesmanagerremix-tutorial/configure4.png)

1. A bal oldali ablaktáblán válassza a **Rendszerbeállítások**  >  **együttműködés a külső rendszerrel** lehetőséget.

    ![A "System Settings" és a "együttműködés a külső rendszerrel" hivatkozás](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. A **külső rendszerrel való együttműködés** ablakban válassza az **SAML** lehetőséget.

    !["A külső rendszerrel való együttműködés" ablak](./media/esalesmanagerremix-tutorial/configure6.png)

1. Az **SAML-hitelesítés beállítása** területen tegye a következőket:

    ![Az "SAML hitelesítési beállítás" szakasz](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Jelölje be a **számítógép verziójának** jelölőnégyzetet.
    
    b. Az **együttműködési elem** szakasz legördülő listájában válassza az **e-mail** lehetőséget.

    c. Az **együttműködési elem** mezőben illessze be a korábban a Azure Portalból másolt jogcím értékét (azaz **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** ).

    d. A **kiállító (entitás azonosítója)** mezőben illessze be azt az azonosító értéket, amelyet korábban a Azure Portal **E Sales Manager Remix tartománya és URL-címei** szakaszban másolt.

    e. A letöltött tanúsítvány Azure Portal való feltöltéséhez válassza a **fájl kiválasztása** lehetőséget.

    f. Az **ID Provider bejelentkezési URL-címe** mezőbe illessze be az SAML egyszeri bejelentkezési szolgáltatás URL-címét, amelyet korábban a Azure Portal másolt.

    : Az **Identity Provider kijelentkezési URL-címe** mezőben illessze be a korábban a Azure Portalban másolt kijelentkezési URL-értéket.

    h. Válassza a **beállítás kész** lehetőséget.

> [!TIP]
> Az alkalmazás beállítása során az előző utasítások tömör változatát olvashatja a [Azure Portal](https://portal.azure.com). Miután hozzáadta az alkalmazást a **Active Directory**  >  **Enterprise Applications** szakaszban, válassza az **egyszeri bejelentkezés** fület, majd nyissa meg a beágyazott dokumentációt a **konfiguráció** szakaszban alul. További információ a beágyazott dokumentációs szolgáltatásról: az [Azure ad Embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban a Azure Portal a következő módon hozza létre a Britta Simon (tesztelés) felhasználóját:

![Azure AD-tesztkörnyezet létrehozása][100]

1. A Azure Portal a bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory hivatkozás](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Az aktuális felhasználók listájának megjelenítéséhez válassza a **felhasználók és csoportok**  >  **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. A **minden felhasználó** ablak tetején válassza a **Hozzáadás** lehetőséget.

    ![A Hozzáadás gomb](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Megnyílik a **felhasználó** ablak.

1. A **felhasználó** ablakban tegye a következőket:

    ![A felhasználói ablak](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. A **név** mezőbe írja be a következőt: **BrittaSimon**.

    b. A **Felhasználónév** mezőbe írja be a Simon Britta felhasználói e-mail-címét.

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd jegyezze fel a **jelszó** mezőben megjelenő értéket.

    d. Válassza a **Létrehozás** lehetőséget.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>E Sales Manager Remix tesztelési felhasználó létrehozása

1. Jelentkezzen be az E Sales Manager Remix-alkalmazásba rendszergazdaként.

1. Válassza ki a **rendszergazda menüt** a jobb felső menüben.

    ![E Sales Manager Remix-konfiguráció](./media/esalesmanagerremix-tutorial/configure4.png)

1. Válassza ki **a vállalati beállítások**  >  **karbantartását a részlegek és alkalmazottak számára**, majd válassza a **regisztrált alkalmazottak** lehetőséget.

    ![Az "alkalmazottak regisztrálva" lap](./media/esalesmanagerremix-tutorial/user1.png)

1. Az **új alkalmazott regisztrálása** szakaszban tegye a következőket:
    
    ![Az "új alkalmazott regisztrálása" szakasz](./media/esalesmanagerremix-tutorial/user2.png)

    a. Az **alkalmazott neve** mezőbe írja be a felhasználó nevét (például **Britta**).

    b. Fejezze be a fennmaradó kötelező mezőket.
    
    c. Ha engedélyezi az SAML-t, a rendszergazda nem tud bejelentkezni a bejelentkezési oldalról. Adja meg a rendszergazdai bejelentkezési jogosultságokat a felhasználónak a rendszergazdai **Bejelentkezés** jelölőnégyzet bejelölésével.

    d. Válassza a **regisztráció** lehetőséget.

1. A jövőben a rendszergazdai jogosultsággal rendelkező felhasználóként jelentkezzen be rendszergazdaként, majd a jobb felső sarokban válassza a **rendszergazda menüt**.

    ![A "felügyeleti menü" parancs](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban az E Sales Manager Remixekhez való hozzáférés biztosításával lehetővé teszi, hogy Simon felhasználó Britta az Azure egyszeri bejelentkezés használatára. Ehhez tegye a következőket: 

![A felhasználói szerepkör kiosztása][200] 

1. A Azure Portal nyissa meg az **alkalmazások** nézetet, nyissa meg a **címtár** nézetet, majd válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.

    ![A "Nagyvállalati alkalmazások" és az "összes alkalmazás" hivatkozás][201] 

1. Az **alkalmazások** listában válassza az **E Sales Manager Remix** lehetőséget.

    ![Az E Sales Manager Remix hivatkozása](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. A bal oldali ablaktáblán válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás][202]

1. Válassza a **Hozzáadás** lehetőséget, majd a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** ablak **felhasználók** listájában válassza a **Britta Simon** elemet.

1. Kattintson a **Kiválasztás** gombra.

1. A **hozzárendelés hozzáadása** ablakban válassza a **hozzárendelés** lehetőséget.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor kiválasztja az E Sales Manager Remix csempét a hozzáférési panelen, automatikusan be kell jelentkeznie az E Sales Manager Remix alkalmazásba.

További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md). 

## <a name="additional-resources"></a>További források

* [Az SaaS-alkalmazások Azure Active Directory-vel való integrálását ismertető oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png