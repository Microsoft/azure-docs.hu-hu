---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Serraview-terület kihasználtsága szoftveres megoldásokkal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Serraview terület kihasználtsági megoldásai között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.openlocfilehash: 6c31a20b527dd6f4741d00b8306c0f88b27f2f6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92672839"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-serraview-space-utilization-software-solutions"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Serraview-terület kihasználtságát biztosító szoftveres megoldásokkal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Serraview-terület kihasználtsági szoftveres megoldásait Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Serraview-terület kihasználtsági szoftveres megoldásait, a következőket teheti:

* Az Azure AD azon vezérlője, aki hozzáfér a Serraview lemezterület-kihasználási szoftveres megoldásokhoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy az Azure AD-fiókjával Serraview a lemezterület-felhasználási szoftverek megoldásait.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Serraview az egyszeri bejelentkezés (SSO) engedélyezett előfizetését.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Serraview lemezterület-kihasználási megoldásai támogatják **az SP és a identitásszolgáltató** által kezdeményezett SSO-t

* Miután konfigurálta a Serraview lemezterület-kihasználási szoftveres megoldásait, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-serraview-space-utilization-software-solutions-from-the-gallery"></a>Serraview lemezterület-kihasználási szoftveres megoldások hozzáadása a katalógusból

A Serraview lemezterület-kihasználási szoftveres megoldások Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Serraview a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Serraview terület kihasználtsága szoftveres megoldások** kifejezést a keresőmezőbe.
1. Válassza ki a **Serraview** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-serraview-space-utilization-software-solutions"></a>Az Azure AD SSO konfigurálása és tesztelése a Serraview lemezterület-kihasználási szoftveres megoldásaihoz

Konfigurálja és tesztelje az Azure AD SSO-t Serraview lemezterület-felhasználási szoftveres megoldásokkal egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Serraview lemezterület-kihasználási szoftveres megoldásaiban.

Az Azure AD SSO konfigurálásához és teszteléséhez Serraview lemezterület-felhasználási megoldásokkal végezze el a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Serraview-terület kihasználtsági szoftveres megoldásának konfigurálása](#configure-serraview-space-utilization-software-solutions-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Serraview lemezterület-kihasználási szoftveres megoldások tesztelési felhasználóval](#create-serraview-space-utilization-software-solutions-test-user)** , hogy a B. Simon párja legyen a Serraview-terület kihasználtsági megoldásaiban, amelyek a felhasználó Azure ad-beli képviseletéhez kapcsolódnak.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Serraview terület kihasználtsága szoftver megoldások** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `urn:Serraview:<SERRAVIEW_IDENTIFIER>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.serraview.com/SAML/AssertionConsumerService.aspx`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<SUBDOMAIN>.serraview.com/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek megszerzéséhez lépjen kapcsolatba a [Serraview-terület kihasználtsága szoftveres megoldásokkal](mailto:svprodops@serraview.com) foglalkozó ügyfélszolgálatával. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Serraview-terület kihasználtságának beállítása** című szakaszban a követelmények alapján másolja ki a megfelelő URL-címeket.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Serraview lemezterület-kihasználási szoftveres megoldásokhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Serraview terület kihasználtsága szoftveres megoldások** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-serraview-space-utilization-software-solutions-sso"></a>A Serraview-terület kihasználtságát segítő szoftveres megoldások egyszeri bejelentkezésének konfigurálása

Ha az egyszeri bejelentkezést a **Serraview terület kihasználtsága szoftveres megoldások** oldalon szeretné konfigurálni, el kell küldenie a letöltött **összevonási METAADATOKat tartalmazó XML** -fájlt és a megfelelő másolt url-címeket a Azure Portal a [Serraview szoftveres megoldások támogatási csapatához](mailto:svprodops@serraview.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-serraview-space-utilization-software-solutions-test-user"></a>Serraview-terület kihasználtsága szoftveres megoldások tesztelése felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Serraview Space-felhasználási szoftveres megoldásokban. A [Serraview Space kihasználtsági szoftveres megoldások támogatási csapatának](mailto:svprodops@serraview.com) használata a felhasználók hozzáadásához a Serraview-terület kihasználtsága szoftver megoldások platformján. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Serraview terület kihasználtsága szoftveres megoldások csempére kattint, automatikusan be kell jelentkeznie a Serraview-terület kihasználtsági szoftveres megoldásaiba, amelyekhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Próbálja ki a Serraview az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)