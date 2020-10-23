---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a egyetértett utazással és költségekkel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory között, és egyetért az utazással és a költségekkel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.openlocfilehash: 525c70c18354e35998e564680c68a975bdb3ec54
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455442"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a egyetértett utazással és költségekkel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a belefoglalt utazási és költségeiket Azure Active Directory (Azure AD-val). Az Azure AD-vel való együttműködés és költségek integrálásával a következőket teheti:

* A hozzáférés az Azure AD-ben, amely az utazáshoz és a költségekhez fér hozzá.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy az Azure AD-fiókjával való részvételre és a költségekre is megtartsák
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egyetért az utazási és a költségek előfizetésével.
* A "céges rendszergazda" szerepkör az Ön által birtokolt felhasználói fiókban. Ellenőrizheti, hogy rendelkezik-e a megfelelő hozzáféréssel az [SSO Self-Service eszköztől](https://www.concursolutions.com/nui/authadmin/ssoadmin). Ha nem rendelkezik hozzáféréssel, lépjen kapcsolatba a partneri támogatással vagy a megvalósítás projekt kezelőjével. 

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését végzi.

* Az utazás és a költségek egyetértenek a **identitásszolgáltató** és az **SP** által kezdeményezett SSO-val
* Az utazás és a költségek egyetértenek az egyszeri bejelentkezés tesztelésével mind az üzemi, mind a megvalósítási környezetben 

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték a következő három régióban: USA, EMEA és Kína. Így csak egy példány konfigurálható az egyes bérlők mindegyik régiójában. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>A katalógusban való megváltozások és költségek hozzáadása

Ha úgy szeretné konfigurálni az integrációt, hogy egyetért az Azure AD-val való utazással és költségekkel, hozzá kell adnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **egybeesik Travel és költségelszámolás** a keresőmezőbe.
1. Válassza az **utazás és költségek** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>Azure AD-beli egyszeri bejelentkezés konfigurálása és tesztelése a megkerülő utazáshoz és költségekhez

Konfigurálja és tesztelje az Azure AD SSO-t, és használja a " **B. Simon**" nevű tesztet. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között, amely egyetért az utazással és a költségekkel kapcsolatban.

Ha az Azure AD SSO-t az utazással és a költségekkel együtt szeretné konfigurálni és tesztelni, végezze el a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A " **[egyetértő utazás és költség SSO" beállítása](#configure-concur-travel-and-expense-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre belefoglalt utazási és költségelszámolás-tesztelési felhasználót](#create-concur-travel-and-expense-test-user)** – hogy a B. Simon partnere a felhasználó Azure ad-beli képviseletéhez kapcsolódó utazási és költségekkel rendelkezzen.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **utazás és költség** alkalmazás integrációja oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfigurációs** szakaszban az alkalmazás előre konfigurálva van a **identitásszolgáltató** által kezdeményezett módban, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Save (Mentés** ) gombra kattintva.

    > [!NOTE]
    > Az azonosító (Entity ID) és a válasz URL-címe (a fogyasztói szolgáltatás URL-címe) a régióra jellemző. Válasszon a saját egyetértő entitás adatközpontja alapján. Ha nem ismeri az Ön egyetértő entitásának adatközpontját, vegye fel a kapcsolatot a egyetértek támogatási szolgálatával. 

5. Az **egyszeri Sign-On beállítása az SAML-vel** lapon a beállítások szerkesztéséhez kattintson a Szerkesztés/toll ikonra a **felhasználói attribútumhoz** . Az egyedi felhasználói azonosítónak meg kell egyeznie a felhasználók login_idával. A **User. userPrincipalName** általában módosítania kell a User. **mail**.

    ![Felhasználói attribútum szerkesztése](common/edit-attribute.png)

6. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a metaadatok letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az utazás és a költségek megadásával való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **egyetért az utazás és a költség**lehetőséggel.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-concur-travel-and-expense-sso"></a>A "egyetértő" utazás és a költségek egyszeri bejelentkezésének konfigurálása

1. Ha be szeretné állítani az egyszeri bejelentkezést a **egyetértő utazásra és a költségekre** , fel kell töltenie a letöltött **összevonási metaadatok XML-fájlját** az [SSO Self-Service eszköznek](https://www.concursolutions.com/nui/authadmin/ssoadmin) való belépéshez és a "vállalati rendszergazda" szerepkörrel rendelkező fiókkal való bejelentkezéshez. 

1. Kattintson a **Hozzáadás** parancsra.
1. Adja meg a identitásszolgáltató egyéni nevét, például: "Azure AD (US)". 
1. Kattintson az **XML-fájl feltöltése** elemre, és csatolja a korábban letöltött **összevonási METAADATOKAT tartalmazó XML-** fájlt.
1. A módosítás mentéséhez kattintson a **metaadatok hozzáadása** elemre.

    ![Az egyszeri bejelentkezéses önkiszolgáló eszköz képernyőképe](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>A Create egyetért az utazási és a költségek tesztelése felhasználóval

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre, és egyetért az utazással és a költségekkel. A egyetértő támogatási csapattal együttműködve veheti fel a felhasználókat a egyetértett utazási és költség platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

> [!NOTE]
> B. Simon egyetértett bejelentkezési azonosítójának meg kell egyeznie a B. Simon egyedi azonosítójával az Azure AD-ben. Ha például a B. Simon Azure AD egyedi termékazonosító `B.Simon@contoso.com` . B. Simon egyetért a bejelentkezési azonosítóval `B.Simon@contoso.com` is. 

## <a name="configure-concur-mobile-sso"></a>Az egybeesik Mobile SSO konfigurálása
A megtagadott mobil egyszeri bejelentkezés engedélyezéséhez meg kell adnia a támogatási csoport **felhasználói hozzáférésének URL-címét**. Kövesse az alábbi lépéseket a **felhasználói hozzáférés URL-címének** lekéréséhez az Azure ad-ből:
1. **Vállalati alkalmazások** ugrása
1. Kattintson **a egyetértek utazási és költségek** elemre.
1. Kattintson a **Tulajdonságok** elemre.
1. **Felhasználói hozzáférési URL-cím** másolása és az URL-cím megadása a támogatás megadásához

> [!NOTE]
> Self-Service az SSO konfigurálására szolgáló beállítás nem érhető el, ezért a bevonási támogatással rendelkező csapattal dolgozhat a mobil egyszeri bejelentkezés engedélyezésével. 

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az egyezményes utazás és ráfordítás csempére kattint, automatikusan be kell jelentkeznie a egyetértett utazásra és költségekre, amelyekhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Próbálja ki az utazást és a költségeket az Azure AD-vel](https://aad.portal.azure.com/)