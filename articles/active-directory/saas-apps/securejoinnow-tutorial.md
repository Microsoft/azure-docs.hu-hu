---
title: 'Oktatóanyag: Azure Active Directory integráció a SecureW2 JoinNow-összekötővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SecureW2 JoinNow-összekötő között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: 024de5acfbc4e6c4aaa510de84c0bceb6bde2b3d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92672960"
---
# <a name="tutorial-integrate-securew2-joinnow-connector-with-azure-active-directory"></a>Oktatóanyag: a SecureW2 JoinNow-összekötő integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a SecureW2 JoinNow-összekötőt Azure Active Directory (Azure AD) használatával. Ha integrálja az SecureW2 JoinNow-összekötőt az Azure AD-vel, a következőket teheti:

* A SecureW2 JoinNow-összekötőhöz hozzáférő Azure AD-beli vezérlés.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezzenek a SecureW2 JoinNow-Összekötőbe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SecureW2 JoinNow-összekötő egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* SecureW2 JoinNow-összekötő támogatja az **SP** által KEZDEMÉNYEZett SSO-t





## <a name="adding-securew2-joinnow-connector-from-the-gallery"></a>SecureW2 JoinNow-összekötő hozzáadása a gyűjteményből

A SecureW2 JoinNow-összekötő Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a SecureW2 JoinNow-összekötőt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **SecureW2 JoinNow-összekötő** kifejezést a keresőmezőbe.
1. Válassza ki a **SecureW2 JoinNow-összekötőt** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a SecureW2 JoinNow-összekötővel egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a SecureW2 JoinNow-összekötőben.

Az Azure AD SSO és a SecureW2 JoinNow-összekötő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. A **[SecureW2 JoinNow-összekötő egyszeri bejelentkezésének konfigurálása](#configure-securew2-joinnow-connector-sso)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre SecureW2 JoinNow-összekötőt](#create-securew2-joinnow-connector-test-user)** , amely a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-hez tartozó SecureW2 JoinNow-összekötővel rendelkezik.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **SecureW2 JoinNow-összekötő** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<organization-identifier>-auth.securew2.com/auth/saml/SSO`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<organization-identifier>-auth.securew2.com/auth/saml`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez forduljon a [SecureW2 JoinNow-összekötő ügyfél-támogatási csapatához](mailto:support@securew2.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **SecureW2 JoinNow-összekötő beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-securew2-joinnow-connector-sso"></a>SecureW2 JoinNow-összekötő egyszeri bejelentkezésének konfigurálása

Ha az egyszeri bejelentkezést a **SecureW2 JoinNow-összekötő** oldalon szeretné konfigurálni, akkor a letöltött **METAADATOKat tartalmazó XML** -fájlt és a megfelelő másolt URL-címeket el kell küldenie az Azure Portalról a [SecureW2 JoinNow-összekötő támogatási csapatához](mailto:support@securew2.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.
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

Ebben a szakaszban az SecureW2 JoinNow-összekötő elérésének biztosításával engedélyezi a B. Simon számára az Azure egyszeri bejelentkezés használatát.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **SecureW2 JoinNow-összekötő** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-securew2-joinnow-connector-test-user"></a>SecureW2 JoinNow-összekötő tesztelési felhasználójának létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a SecureW2 JoinNow-összekötőben. Együttműködik a [SecureW2 JoinNow-összekötő támogatási csapatával](mailto:support@securew2.com) a felhasználók hozzáadásához a SecureW2 JoinNow-összekötő platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a SecureW2 JoinNow-összekötő csempére kattint, automatikusan be kell jelentkeznie a SecureW2 JoinNow-Összekötőbe, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)