---
title: 'Oktatóanyag: Azure Active Directory integráció az elme Tools Toolkit használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az elme Tools eszközkészlet között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.openlocfilehash: 57598ea22874cfba772f6cf0e6b2ba779ef7c482
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92448401"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>Oktatóanyag: Azure Active Directory integráció az elme Tools Toolkit használatával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az elme Tools Toolkitot Azure Active Directory (Azure AD) használatával.

Ezzel az integrációval a következőket teheti:

* Az elme Tools eszközkészlethez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az elme Tools Toolkitbe (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory](../manage-apps/what-is-single-sign-on.md)használatával című témakört.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az elme Tools Toolkit segítségével való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az elme Tools Toolkit-előfizetés engedélyezve van az egyszeri bejelentkezéssel (SSO).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az elme Tools Toolkit támogatja az SP által kezdeményezett egyszeri bejelentkezést.
* Az elme Tools Toolkit az igény szerinti felhasználói üzembe helyezést is támogatja.
* Az elme-eszközök eszközkészletének konfigurálása után kényszerítheti a munkamenet-vezérlést. Ez a vezérlő valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mind-tools-toolkit-from-the-gallery"></a>Az elme Tools eszközkészletének hozzáadása a katalógusból

Az elme Tools Toolkit Azure AD-be való integrálásának konfigurálásához az elme Tools eszközkészletet kell hozzáadnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal szélső navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban adja meg az **elme Tools Toolkit** kifejezést a keresőmezőbe.
1. Válassza az **elme Tools Toolkit** lehetőséget a keresési eredmények közül, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az elme Tools Toolkit használatával konfigurálhatja és tesztelheti egy **B. Simon** nevű tesztelési felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó az elme Tools eszközkészletben.

Az Azure AD egyszeri bejelentkezés az elme Tools Toolkit használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Állítsa be az elme Tools TOOLKIT SSO](#configure-mind-tools-toolkit-sso)** -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. **[Hozzon létre egy elme Tools Toolkit-teszt felhasználót](#create-a-mind-tools-toolkit-test-user)** , hogy a B. Simon az elme Tools Toolkit tagja legyen. Ez a partner a felhasználó Azure AD-képviseletéhez van társítva.
1. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban a következő lépéseket követve konfigurálja az Azure AD egyszeri bejelentkezést az elme Tools Toolkit segítségével:

1. Az [Azure Portal](https://portal.azure.com/)az **elme Tools Toolkit** Application Integration oldalon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![A kezelés szakasz egyszeri bejelentkezéssel kiemelve](common/select-sso.png)

1. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Az egyszeri bejelentkezési módszer kiválasztása párbeszédpanel, amely az SAML-ra van kijelölve](common/select-saml-option.png)

1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

    ![Az egyszeri Sign-On beállítása az SAML-oldallal lapon az alapszintű SAML-konfigurációhoz tartozó ceruza ikont kiemelve](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakasz **bejelentkezési URL-címe** mezőjébe írja be a mintát tartalmazó URL-címet `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId>` .

    > [!NOTE]
    > A **bejelentkezési URL-cím** értéke nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez vegye fel a kapcsolatot az [elme Tools Toolkit ügyfél-támogatási csapatával](mailto:support@goodpractice.com) .

1. Az **egyszeri Sign-On az SAML-vel** lapon lépjen az **SAML-aláíró tanúsítvány** szakaszra. Az **összevonási metaadatok XML-fájljának** jobb oldalán válassza a **Letöltés** lehetőséget az XML-szöveg letöltéséhez, és mentse a számítógépre. Az XML-tartalom a kiválasztott beállításoktól függ.

    ![Az SAML aláíró tanúsítvány szakasza az összevonási metaadatok XML-fájlja mellett Kiemelt letöltéssel](common/metadataxml.png)

1. Az **elme-eszközök beállítása eszközkészlet** szakaszban másolja az alábbi URL-címek közül a kívántat.

    * **Bejelentkezési URL-cím**

    * **Azure AD-azonosító**

    * **Kijelentkezési URL-cím**

    ![Az elme-eszközök eszközkészletének beállítása szakasz, a konfigurációs URL-címek kiemelve](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portalban:

1. A Azure Portal bal szélső oldalán válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.
1. A képernyő felső részén válassza az **új felhasználó** lehetőséget.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A név mezőbe írja be a **B. Simon** **nevet** .  
   1. A **Felhasználónév** mezőbe írja be **B.Simon@**_cégestartomány_**.** _bővítmény_. Például: B.Simon@contoso.com.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezi a B. Simon számára az Azure egyszeri bejelentkezés használatát az elme Tools eszközkészlethez való hozzáférés megadásával.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **elme-eszközök eszközkészletet**.
1. Az alkalmazás áttekintés lapján lépjen a **kezelés** szakaszra, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A kezelés szakaszban Kiemelt felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza a **Felhasználó hozzáadása** elemet. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

   ![A felhasználók és csoportok ablak, a felhasználó hozzáadása kiemelve](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza ki az **B. Simon** elemet a felhasználók listából. Ezután kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából. Ezután kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-mind-tools-toolkit-sso"></a>Az elme Tools Toolkit egyszeri bejelentkezésének konfigurálása

Az **elme Tools Toolkit** oldalán az egyszeri bejelentkezés konfigurálásához küldje el a letöltött **összevonási metaadatok XML-** szövegét és a korábban átmásolt URL-címeket az [elme Tools Toolkit támogatási csapatának](mailto:support@goodpractice.com). Ezt a beállítást úgy konfigurálja, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

### <a name="create-a-mind-tools-toolkit-test-user"></a>Az elme Tools Toolkit-teszt felhasználójának létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre az elme Tools Toolkit-ben.

Az elme Tools Toolkit az igény szerinti üzembe helyezést is támogatja, ami alapértelmezés szerint engedélyezve van. Ebben a szakaszban nem végezhető művelet. Ha egy felhasználó még nem létezik az elme Tools Toolkit-ben, akkor a rendszer létrehoz egy újat, amikor megpróbál hozzáférni az elme Tools eszközkészlethez.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a saját alkalmazások portál használatával.

Amikor kiválasztja a saját alkalmazások portálon az elme Tools Toolkit csempét, automatikusan bejelentkezik az elmés Tools eszközkészletbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a saját alkalmazások portálján: [Bevezetés a My apps portálra](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok az SaaS-alkalmazások integrálásához Azure Active Directory](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Az elme Tools eszközkészletének kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [Az elme Tools Toolkit speciális láthatósággal és vezérlőkkel való ellátása](/cloud-app-security/proxy-intro-aad)