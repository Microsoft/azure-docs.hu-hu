---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Trelica | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Trelica között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: a674f5f653ad420ab8f28ff73c6b86f9c18b154e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92517752"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trelica"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Trelica

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Trelica a Azure Active Directory (Azure AD) szolgáltatással.

Ezzel az integrációval a következőket teheti:

* A Trelica-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Trelica az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory](../manage-apps/what-is-single-sign-on.md)használatával című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezést (SSO) engedélyező Trelica-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Trelica támogatja a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést.
* A Trelica támogatja az igény szerinti felhasználói üzembe helyezést.
* A Trelica konfigurálása után kényszerítheti a munkamenet-vezérlést. Ez a vezérlő valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trelica-from-the-gallery"></a>Trelica hozzáadása a gyűjteményből

A Trelica Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Trelica a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal szélső navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban adja meg a **Trelica** kifejezést a keresőmezőbe.
1. A keresési eredmények közül válassza a **Trelica** lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trelica"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Trelica

Konfigurálja és tesztelje az Azure AD SSO-t a Trelica egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a Trelica-ben.

Az Azure AD SSO és a Trelica konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Konfigurálja a TRELICA SSO](#configure-trelica-sso)** -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. **[Hozzon létre egy Trelica-tesztelési felhasználót](#create-a-trelica-test-user)** , hogy a B. Simon párja legyen a Trelica. Ez a partner a felhasználó Azure AD-képviseletéhez van társítva.
1. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO-t a Azure Portalban:

1. A [Azure Portal](https://portal.azure.com/) **Trelica** alkalmazás-integráció lapján lépjen a **kezelés** szakaszra. Válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Az egyszeri Sign-On beállítása az SAML-oldallal lapon az alapszintű SAML-konfigurációhoz tartozó ceruza ikont kiemelve](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon adja meg a következő értékeket:

    1. Az **azonosító** mezőben adja meg az URL-címet **https://app.trelica.com** .

    1. A **Válasz URL-címe** mezőbe írja be a mintát tartalmazó URL-címet `https://app.trelica.com/Id/Saml2/<CUSTOM_IDENTIFIER>/Acs` .

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. Frissítse ezt az értéket a tényleges válasz URL-címmel (más néven ACS).
    > Ezt úgy érheti el, ha bejelentkezik a Trelica-be, majd a [SAML Identity Providers konfigurációs oldalra](https://app.trelica.com/Admin/Profile/SAML) (rendszergazdai > fiók > SAML). Kattintson az **állítási fogyasztói szolgáltatás (ACS) URL-címe** melletti Másolás gombra, hogy ezt a vágólapra helyezze, és készen áll a **Válasz URL** szövegmezőbe való BEILLESZTÉSre az Azure ad-ben.
    > Ha kérdése van, olvassa el a [Trelica súgóját](https://docs.trelica.com/admin/saml/azure-ad) , vagy vegye fel a kapcsolatot a Trelica ügyfélszolgálati [csapatával](mailto:support@trelica.com) .

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![Az SAML aláíró tanúsítvány szakasza, az alkalmazás-összevonási metaadatok URL-címe melletti másolás gombbal.](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.
1. A képernyő felső részén válassza az **új felhasználó** lehetőséget.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A név mezőbe írja be a **B. Simon** **nevet** .
   1. A **Felhasználónév** mezőbe írja be **B.Simon@**_cégestartomány_**.** _bővítmény_. Például: B.Simon@contoso.com.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezi a B. Simon számára az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Trelica.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Trelica** lehetőséget.
1. Az alkalmazás áttekintés lapján lépjen a **kezelés** szakaszra, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A kezelés szakaszban Kiemelt felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza a **Felhasználó hozzáadása** elemet. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

   ![A felhasználók és csoportok ablak, a felhasználó hozzáadása kiemelve](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza ki az **B. Simon** elemet a felhasználók listából. Ezután kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából. Ezután kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-trelica-sso"></a>Trelica SSO konfigurálása

Ha az egyszeri bejelentkezést az **Trelica** oldalon szeretné konfigurálni, lépjen a [SAML Identity Providers konfigurációs oldalra](https://app.trelica.com/Admin/Profile/SAML) (rendszergazdai > fiók > SAML). Kattintson az **új** gombra. Adja meg az **Azure ad** nevet, és válassza ki a metaadatokat a metaadat típusa **URL-címéből** . Illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet az Azure ad-ből kapott a Trelica **metaadat-URL** mezőjébe.

Ha kérdése van, olvassa el a [Trelica súgóját](https://docs.trelica.com/admin/saml/azure-ad) , vagy vegye fel a kapcsolatot a Trelica ügyfélszolgálati [csapatával](mailto:support@trelica.com) .

### <a name="create-a-trelica-test-user"></a>Trelica-teszt felhasználó létrehozása

A Trelica támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nem végezhető művelet. Ha egy felhasználó még nem létezik a Trelica-ben, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a saját alkalmazások portál használatával.

Amikor kiválasztja a Trelica csempét a saját alkalmazások portálon, automatikusan bejelentkezik arra a Trelica, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a saját alkalmazások portálján: [Bevezetés a My apps portálra](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok az SaaS-alkalmazások integrálásához Azure Active Directory](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Trelica kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A Trelica és a speciális láthatóság és vezérlők elleni védelem](/cloud-app-security/proxy-intro-aad)