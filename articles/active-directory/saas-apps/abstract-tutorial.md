---
title: 'Oktatóanyag: Azure Active Directory az absztrakt integrációval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és absztrakt között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: e1c3236c4c1957b4d0daee8d30c71f03fb8674dd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587825"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Oktatóanyag: absztrakt integráció a Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az absztraktot Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja az absztraktot, a következőket teheti:

* A vezérlő az Azure AD-ben, hogy ki férhet hozzá az absztrakthoz.
* Lehetővé teheti a felhasználók számára, hogy az Azure AD-fiókokkal való absztrakt bejelentkezve automatikusan bejelentkezzenek.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Absztrakt egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Absztrakt **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO

## <a name="adding-abstract-from-the-gallery"></a>Absztrakt hozzáadása a katalógusból

Az absztrakt Azure AD-be való integrálásának konfigurálásához a katalógusból absztraktot kell hozzáadnia a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a szövegmezőbe az **absztrakt** kifejezést.
1. Válassza az **absztrakt** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Az Azure AD SSO az absztrakt használatával történő konfigurálását és tesztelését egy **B. Simon** nevű tesztelési felhasználó segítségével végezheti el. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között absztrakt módon.

Az Azure AD SSO absztrakt használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. **[ABSZTRAKT SSO konfigurálása](#configure-abstract-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Absztrakt tesztelési felhasználó létrehozása](#create-abstract-test-user)** – a Britta Simon-nek a felhasználó Azure ad-képviseletéhez kapcsolódó, absztraktként megadott párja.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **absztrakt** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfigurációs** szakaszban az alkalmazás előre konfigurálva van a **identitásszolgáltató** által kezdeményezett módban, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Save (Mentés** ) gombra kattintva.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:  `https://app.abstract.com/signin`

4. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Absztrakt SSO konfigurálása

Győződjön meg arról, hogy a (z) és a (z) Azure Portal beolvasása `App Federation Metadata Url` `Azure AD Identifier` szükséges, mivel az SSO-t absztraktként kell konfigurálnia.

Ezeket az információkat az **egyszeri Sign-On beállítása SAML** -oldallal című oldalon találja:

* A az `App Federation Metadata Url` **SAML aláíró tanúsítvány** szakaszban található.
* A a ( `Azure AD Identifier` **absztrakt beállítása** ) szakaszban található.


Most már készen áll az SSO konfigurálására Absztrakton:

>[!Note]
>A szervezeti rendszergazdai fiókkal kell hitelesítenie az SSO-beállítások absztrakt elérését.

1. Nyissa meg az [absztrakt webalkalmazást](https://app.abstract.com/).
2. Lépjen az **engedélyek** lapra a bal oldali sávban.
3. Az **SSO konfigurálása** szakaszban adja meg a **metaadatok URL-címét** és az **entitás azonosítóját**.
4. Adja meg az esetlegesen felmerülő manuális kivételeket. A manuális kivételek szakaszban felsorolt e-mailek megkerülik az egyszeri bejelentkezést, és e-mailben és jelszóval jelentkezhetnek be. 
5. Kattintson a **Save Changes** (Módosítások mentése) gombra.

>[!Note] 
>Az elsődleges e-mail-címeket a manuális kivételek listájában kell használni. Az egyszeri bejelentkezés aktiválása sikertelen lesz, ha az Ön által megadott e-mail egy felhasználó másodlagos e-mail-címe. Ha ez történik, megjelenik egy hibaüzenet, amely a sikertelen fiók elsődleges e-mail-címével jelenik meg. Adja hozzá az elsődleges e-mailt a manuális kivételek listájához, miután meggyőződött arról, hogy ismeri a felhasználót.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezést az absztrakt hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **absztrakt** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-abstract-test-user"></a>Absztrakt tesztelési felhasználó létrehozása

Az egyszeri bejelentkezés tesztelése absztraktként:

1. Nyissa meg az [absztrakt webalkalmazást](https://app.abstract.com/).
2. Lépjen az **engedélyek** lapra a bal oldali sávban.
3. Kattintson **a teszt a saját fiókkal** lehetőségre. Ha a teszt sikertelen, [forduljon a támogatási csapathoz](https://help.abstract.com/hc/).

>[!Note]
>A szervezeti rendszergazdai fiókkal kell hitelesítenie az SSO-beállítások absztrakt elérését.
Ezt a szervezeti rendszergazdai fiókot a Azure Portal absztrakt eleméhez kell rendelni.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az absztrakt csempére kattint, akkor automatikusan be kell jelentkeznie arra az Absztraktra, amelyhez az SSO-t beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)