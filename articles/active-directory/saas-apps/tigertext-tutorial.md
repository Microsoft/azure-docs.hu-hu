---
title: 'Oktatóanyag: Azure Active Directory integráció a TigerConnect Secure Messengerrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a TigerConnect Secure Messenger között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/21/2020
ms.author: jeedes
ms.openlocfilehash: 6831e96ebd8fd2db51d640ff3d93955b1a9f1477
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182155"
---
# <a name="tutorial-azure-active-directory-integration-with-tigerconnect-secure-messenger"></a>Oktatóanyag: Azure Active Directory integráció a TigerConnect Secure Messengerrel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a TigerConnect Secure Messengert Azure Active Directory (Azure AD) használatával.

A TigerConnect Secure Messenger és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a TigerConnect Secure Messengerhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a biztonságos TigerConnect (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Az Azure AD-vel való szolgáltatott szoftverek (SaaS) alkalmazással kapcsolatos részletekért tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció TigerConnect Secure Messengerrel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.
* Egy TigerConnect biztonságos Messenger-előfizetés egyszeri bejelentkezéssel.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben konfigurálja és teszteli, és integrálja az TigerConnect Secure Messengert az Azure AD-vel.

* A TigerConnect Secure Messenger támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A TigerConnect Secure Messenger konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-tigerconnect-secure-messenger-from-the-gallery"></a>TigerConnect Secure Messenger hozzáadása a katalógusból

A TigerConnect biztonságos Messenger Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a TigerConnect Secure Messengert a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **TigerConnect Secure Messenger** kifejezést a keresőmezőbe.
1. Válassza az **TigerConnect Secure Messenger** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD SSO konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az TigerConnect Secure Messengerrel konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a TigerConnect Secure Messengerben.

Az Azure AD egyszeri bejelentkezés TigerConnect Secure Messengerrel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    * **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy a Britta Simon engedélyezze az Azure ad egyszeri bejelentkezés használatát.
1. **[Konfigurálja a TigerConnect Secure Messenger SSO](#configure-tigerconnect-secure-messenger-sso)** -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    * **[Hozzon létre egy TigerConnect Secure Messenger-teszt felhasználót](#create-a-tigerconnect-secure-messenger-test-user)** , hogy a Britta Simon nevű felhasználó a TigerConnect Secure Messengerben, aki a Britta Simon nevű Azure ad-felhasználóhoz van társítva.
1. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a TigerConnect Secure Messengerrel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **TigerConnect Secure Messenger** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    1. A **bejelentkezési URL** -cím mezőbe írjon be egy URL-címet:

       `https://home.tigertext.com`

    1. Az **azonosító (entitás azonosítója)** mezőbe írja be az URL-címet a következő minta használatával:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > Az **azonosító (Entity ID)** érték nem valós. Frissítse ezt az értéket a tényleges azonosítóval. Az érték beszerzéséhez forduljon a [TigerConnect Secure Messenger támogatási csapatához](mailto:prosupport@tigertext.com). A Azure Portal **alapszintű SAML-konfiguráció** paneljén látható mintázatokat is megtekintheti.

1. Az **egyszeri Sign-On beállítása az SAML** -panellel lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget, hogy letöltse az **összevonási metaadatok XML-** fájlját a megadott beállításokból, és mentse a számítógépre.

    ![Az összevonási metaadatok XML-letöltési lehetősége](common/metadataxml.png)

1. A **TigerConnect biztonságos Messenger beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)


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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a TigerConnect Secure Messengerhez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **TigerConnect Secure Messenger** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-tigerconnect-secure-messenger-sso"></a>TigerConnect biztonságos Messenger SSO konfigurálása

Ha az egyszeri bejelentkezést a biztonságos TigerConnect szeretné konfigurálni, el kell küldenie a letöltött összevonási metaadatokat tartalmazó XML-fájlt és a megfelelő másolt URL-címeket a Azure Portal a [TigerConnect Secure Messenger támogatási csapatának](mailto:prosupport@tigertext.com). A TigerConnect Secure Messenger csapata gondoskodni fog arról, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

## <a name="create-a-tigerconnect-secure-messenger-test-user"></a>TigerConnect biztonságos Messenger-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a TigerConnect Secure Messengerben. Működjön együtt a [TigerConnect Secure Messenger támogatási csapatával](mailto:prosupport@tigertext.com) , és adja hozzá a Britta Simon felhasználót a TigerConnect Secure Messengerben. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a saját alkalmazások portál használatával.

Ha a saját alkalmazások portálon a **TigerConnect biztonságos Messenger** elemet választja, akkor automatikusan be kell jelentkeznie a TigerConnect Secure Messenger-előfizetésbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a saját alkalmazások portálján: [alkalmazások elérése és használata a saját alkalmazások portálon](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-vel való integrálására szolgáló oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)

- [A TigerConnect Secure Messenger kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)