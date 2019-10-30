---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Veracode | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Veracode között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcec326ddab1e74f43e1bb7ef446998a40799fd0
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043560"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Veracode

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Veracode a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Veracode-t az Azure AD-vel, a következőket teheti:

* A Veracode-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Veracode az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)használatával című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Veracode egyszeri bejelentkezésre (SSO) alkalmas előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A Veracode támogatja a személyazonosság-szolgáltató által kezdeményezett egyszeri bejelentkezést és igény szerinti felhasználói üzembe helyezést.

## <a name="add-veracode-from-the-gallery"></a>Veracode hozzáadása a gyűjteményből

A Veracode Azure AD-be való integrálásának konfigurálásához adja hozzá a Veracode a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a "Veracode" kifejezést a keresőmezőbe.
1. Válassza az **Veracode** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Veracode

Konfigurálja és tesztelje az Azure AD SSO-t a Veracode egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a Veracode-ben.

Az Azure AD SSO és a Veracode konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    * **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Konfigurálja a VERACODE SSO](#configure-veracode-sso)** -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    * **[Hozzon létre egy Veracode-tesztelési felhasználót](#create-veracode-test-user)** , hogy a Veracode B. Simon párja legyen a felhasználó Azure ad-képviseletéhez társítva.
1. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Veracode** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt. Válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

   ![Képernyőkép az egyszeri bejelentkezés beállításáról az SAML használatával, a ceruza ikon kiemelve](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban az alkalmazás előre konfigurálva van, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. Kattintson a **Mentés** gombra.

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** című szakaszt. A **Letöltés** gombra kattintva letöltheti a tanúsítványt, és mentheti a számítógépre.

    ![Képernyőfelvétel az SAML-aláíró tanúsítványról szakasz, a letöltési hivatkozás kiemelve](common/certificatebase64.png)

1. A Veracode egy adott formátumban várja az SAML-jogkivonatokat, amelyhez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Képernyőkép a felhasználói attribútumok & jogcímek szakaszról](common/default-attributes.png)

1. A Veracode Emellett néhány további attribútumot is vár az SAML-válaszban. Ezek az attribútumok előre fel vannak töltve, de a követelmények szerint áttekinthetők.

    | Név | Forrás attribútum|
    | ---------------| --------------- |
    | FirstName |User. givenName |
    | LastName |felhasználó. vezetéknév |
    | e-mail |User. mail |

1. A **Veracode beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Képernyőkép a Veracode beállítása szakaszról, a konfigurációs URL-címek kiemelve](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Veracode SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Veracode vállalati webhelyre rendszergazdaként.

1. A felső menüben válassza a **beállítások** > **rendszergazda**lehetőséget.
   
    ![Képernyőkép a Veracode felügyeletről, a beállítások ikon és a rendszergazda kiemelve](./media/veracode-tutorial/ic802911.png "Felügyelet")

1. Válassza ki az **SAML** lapot.

1. A **szervezet SAML-beállításai** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel a szervezeti SAML-beállításokról szakasz](./media/veracode-tutorial/ic802912.png "Felügyelet")

    a.  A **kiállító**esetében illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    b. Az **érvényesítési tanúsítványnál**válassza a **fájl kiválasztása** lehetőséget a letöltött tanúsítvány Azure Portal való feltöltéséhez.

    c. Az **Önregisztrációhoz**válassza a **saját regisztráció engedélyezése**lehetőséget.

1. Az **Önregisztráció beállításai** szakaszban hajtsa végre az alábbi lépéseket, majd válassza a **Mentés**lehetőséget:

    ![Képernyőfelvétel az Önregisztráció beállításairól szakasz, különböző lehetőségekkel](./media/veracode-tutorial/ic802913.png "Felügyelet")

    a. Az **új felhasználói aktiváláshoz**válassza a **nincs szükség aktiválásra**lehetőséget.

    b. A **felhasználói adatfrissítések**esetében válassza a **preferencia Veracode felhasználói információk**lehetőséget.

    c. Az **SAML-attribútumok részleteihez**válassza ki a következőket:
      * **Felhasználói szerepkörök**
      * **Házirend rendszergazdája**
      * **Felülvizsgáló**
      * **Biztonsági érdeklődő**
      * **Végrehajtó**
      * **Beküldő**
      * **Creator**
      * **Minden vizsgálat típusa**
      * **Csoport tagsága**
      * **Alapértelmezett csapat**

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** >**felhasználók** > **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:

   1. A **név**mezőbe írja be a következőt: `B.Simon`.  
   1. A **Felhasználónév**mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Válassza a **jelszó megjelenítése**lehetőséget, majd írja le a megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezze a B. Simon számára az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Veracode.

1. A Azure Portal válassza a **vállalati alkalmazások** > **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Veracode**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![Képernyőfelvétel a kezelésről szakasz – Kiemelt felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**elemet. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok**lehetőséget.

    ![Képernyőkép a felhasználók és csoportok oldalról, a felhasználó hozzáadása elem kiemelésével](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanel **felhasználók**területén válassza a **B. Simon**lehetőséget. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés**lehetőséget.

### <a name="create-veracode-test-user"></a>Veracode-tesztelési felhasználó létrehozása

A Veracode való bejelentkezéshez az Azure AD-felhasználókat a Veracode-ben kell kiépíteni. Ez a feladat automatizált, és semmit nem kell manuálisan végrehajtania. A felhasználók szükség esetén automatikusan létrejönnek az első egyszeri bejelentkezés megkísérlése során.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a Veracode által biztosított Veracode felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor kiválasztja a **Veracode** lehetőséget a hozzáférési panelen, automatikusan be kell jelentkeznie arra a Veracode, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Veracode kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)