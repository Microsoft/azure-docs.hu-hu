---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az MS Azure SSO-hozzáféréssel a Ethidex megfelelőségi Office-™hoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és MS Azure SSO-hozzáférés között a Ethidex megfelelőségi iroda™.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: jeedes
ms.openlocfilehash: 7ef219ca147fe96fc65f14bbf3ba6a565adc95ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520914"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az MS Azure SSO-hozzáféréssel a Ethidex-megfelelőségi iroda számára™

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az MS Azure SSO-hozzáférést a Ethidex megfelelőségi iroda™ az Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az MS Azure SSO-hozzáférést a Ethidex megfelelőségi Office-™ az Azure AD-vel, a következőket teheti:

* Az Azure AD-ben a Ethidex megfelelőségi iroda™ához hozzáférő MS Azure SSO-hozzáféréshez férhet hozzá.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az MS Azure SSO-hozzáférésre a Ethidex megfelelőségi iroda™ Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* MS Azure SSO-hozzáférés a Ethidex megfelelőségi iroda™ egyszeri bejelentkezéses (SSO) engedélyezett előfizetéshez.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* MS Azure SSO-hozzáférés a Ethidex megfelelőségi iroda™ támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>MS Azure SSO-hozzáférés hozzáadása a Ethidex megfelelőségi iroda™ a katalógusból

A Ethidex megfelelőségi Office-™ Azure AD-ba történő MS Azure SSO-hozzáférésének konfigurálásához hozzá kell adnia az MS Azure SSO-hozzáférést a Ethidex-megfelelőségi iroda™ a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **MS Azure SSO-hozzáférés Ethidex megfelelőségi iroda™** a keresőmezőbe.
1. Válassza az **MS Azure SSO-hozzáférés lehetőséget a Ethidex megfelelőségi iroda™** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az MS Azure SSO-hozzáféréshez a Ethidex megfelelőségi iroda™

Konfigurálja és tesztelje az Azure AD SSO-t MS Azure SSO-hozzáféréssel a Ethidex megfelelőségi iroda™ egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között, amely MS Azure SSO-hozzáférést biztosít a Ethidex megfelelőségi iroda™.

Az Azure AD SSO konfigurálásához és teszteléséhez MS Azure SSO-hozzáféréssel a Ethidex megfelelőségi iroda™éhez végezze el a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[MS Azure SSO-hozzáférés konfigurálása a Ethidex megfelelőségi iroda egyszeri](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** bejelentkezéséhez – az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
    1. **[MS Azure SSO-hozzáférés létrehozása a Ethidex-megfelelőségi Office-teszt felhasználója](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** számára – hogy a B. Simon-nek megfelelő, MS Azure SSO-hozzáférés a Ethidex megfelelőségi iroda™, amely a felhasználó Azure ad-beli képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **MS Azure SSO-hozzáférés a Ethidex megfelelőségi iroda™** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `com.ethidex.prod.<CLIENTID>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.ethidex.com/saml2/sp/acs/<CLIENTID>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Vegye fel a kapcsolatot [az MS Azure SSO-hozzáféréssel a Ethidex megfelelőségi iroda™ támogatási csapatának](mailto:support@ethidex.com) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. MS Azure SSO-hozzáférés a Ethidex-megfelelőségi iroda™ Application Application az SAML-kijelentéseket egy adott formátumban várja, amelyhez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName** leképezéssel van leképezve. A Ethidex megfelelőségi iroda MS Azure SSO-hozzáférése™ alkalmazás a **User. mail** használatával rendeli hozzá a **NameIdentifier** , ezért az attribútum-hozzárendelést úgy kell szerkesztenie, hogy a **Szerkesztés** ikonra kattint, és megváltoztatja az attribútum-hozzárendelést.

    ![image](common/edit-attribute.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (RAW)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

1. Az **MS Azure SSO-hozzáférés beállítása a Ethidex megfelelőségi irodához™** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban az Azure egyszeri bejelentkezés használatát teszi lehetővé a B. Simon számára, hogy hozzáférést biztosítson az MS Azure SSO-hozzáféréshez a Ethidex megfelelőségi iroda™.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **MS Azure SSO-hozzáférés a Ethidex megfelelőségi iroda™** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>MS Azure SSO-hozzáférés konfigurálása a Ethidex megfelelőségi iroda egyszeri bejelentkezéséhez

Ha be szeretné állítani az egyszeri bejelentkezést az **MS Azure SSO-hozzáféréshez a Ethidex megfelelőségi iroda™** oldalán, el kell küldenie a letöltött **tanúsítványt (RAW)** és a megfelelő másolt url-címeket a Azure Portalból az [MS Azure SSO-hozzáféréshez a Ethidex megfelelőségi iroda™ támogatási csapat számára](mailto:support@ethidex.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>MS Azure SSO-hozzáférés létrehozása a Ethidex-megfelelőségi iroda tesztelési felhasználója számára

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre az MS Azure SSO-hozzáférésben a Ethidex megfelelőségi iroda™. Együttműködik az [MS Azure SSO-hozzáféréssel a Ethidex megfelelőségi iroda™ támogatási csapatának](mailto:support@ethidex.com) , hogy hozzáadja a felhasználókat az MS Azure SSO hozzáférés a Ethidex megfelelőségi iroda™ platformhoz. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az MS Azure SSO-hozzáférés Ethidex megfelelőségi iroda™ csempére kattint, automatikusan be kell jelentkeznie az MS Azure SSO-hozzáférés a Ethidex megfelelőségi Office-™, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [MS Azure SSO-hozzáférés kipróbálása a Ethidex megfelelőségi iroda™ az Azure AD-vel](https://aad.portal.azure.com/)