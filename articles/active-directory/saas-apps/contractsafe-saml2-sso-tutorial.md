---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a ContractSafe egy saml2 SSO-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a ContractSafe egy saml2 SSO között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.openlocfilehash: 2d236b9910e2eda8e574d020544d625a68aefb81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455312"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a ContractSafe egy saml2 SSO-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ContractSafe egy saml2 SSO-t Azure Active Directory (Azure AD-val). Ha integrálja az ContractSafe egy saml2 SSO-t az Azure AD-vel, a következőket teheti:

* Annak szabályozása, hogy ki férhet hozzá a ContractSafe egy saml2 SSO-hoz az Azure AD-ben.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a ContractSafe egy saml2 SSO-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egy ContractSafe egy saml2 egyszeri bejelentkezéses előfizetés engedélyezve.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A ContractSafe egy saml2 SSO támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>ContractSafe egy saml2 SSO hozzáadása a katalógusból

A ContractSafe egy saml2 SSO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ContractSafe egy saml2 SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **ContractSafe egy saml2 SSO** kifejezést a keresőmezőbe.
1. Válassza az **ContractSafe egy SAML2 SSO** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Azure AD SSO konfigurálása és tesztelése ContractSafe egy saml2 SSO-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a ContractSafe egy saml2 SSO-val egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a ContractSafe egy saml2 SSO-ban.

Az Azure AD SSO konfigurálásához és a ContractSafe egy saml2 SSO-val való teszteléséhez hajtsa végre a következő építőelemeket:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.
   * [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad SSO teszteléséhez a **B. Simon** -fiók használatával.
   * [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy **B. Simon** engedélyezze az Azure ad SSO használatát.

1. [Konfigurálja a ContractSafe egy SAML2 SSO](#configure-contractsafe-saml2-sso) -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
   * [Hozzon létre egy ContractSafe-egy SAML2 SSO-tesztelési felhasználót](#create-a-contractsafe-saml2-sso-test-user) , hogy az a felhasználó Azure ad-képviseletéhez kapcsolódó **B. Simon** ContractSafe-egy saml2.
2. Ellenőrizze az [SSO](#test-sso) -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO-t a Azure Portalban:

1. A [Azure Portal](https://portal.azure.com/) **ContractSafe egy saml2 SSO** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az **ALAPszintű SAML-konfiguráció** szerkesztés (toll) ikonját a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon adja meg a következő értékeket a megfelelő mezőkben:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő formátum használatával: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő formátum használatával: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Vegye fel a kapcsolatot a [ContractSafe egy SAML2 SSO-ügyfél támogatási csapatával](mailto:support@contractsafe.com) az értékek lekéréséhez. A Azure Portal az **alapszintű SAML-konfiguráció** szakaszának formátumait is megtekintheti.

1. A ContractSafe egy saml2 SSO adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Gyakori alapértelmezett attribútumok](common/default-attributes.png)

1. Az alapértelmezett attribútumok mellett a ContractSafe egy saml2 SSO-alkalmazás néhány további attribútumot vár az SAML-válaszban való visszatéréshez. Ezek az attribútumok előre ki vannak töltve, de a követelmények szerint áttekinthetők. Az alábbi listában a további attribútumok láthatók.

    | Name | Forrás attribútum|
    | ---------------| --------------- |
    | EmailName | User. userPrincipalName |
    | e-mail | User. onpremisesuserprincipalname |

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját**. Válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez, majd mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **ContractSafe egy saml2-SSO beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal **B. Simon** néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget. Válassza a **felhasználók** lehetőséget, majd válassza **a minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg az e-mail-címet a `username@companydomain.extension` formátumban. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

## <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a **B. Simon** számára engedélyezi az Azure SSO használatát azáltal, hogy hozzáférést biztosít a CONTRACTSAFE egy saml2 SSO-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **ContractSafe egy saml2 egyszeri bejelentkezés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, majd válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

   ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza ki az **B. Simon** elemet a **felhasználók** listából. Ezután kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Ezután kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-contractsafe-saml2-sso"></a>ContractSafe egy saml2 SSO konfigurálása

Ha az SSO-t a **ContractSafe egy SAML2 SSO** oldalon szeretné konfigurálni, el kell küldenie a letöltött **összevonási metaadatokat tartalmazó XML-fájlt** és a megfelelő másolt url-címeket a Azure Portal a [ContractSafe egy saml2 SSO támogatási csapatának](mailto:support@contractsafe.com). A csapat feladata az SAML SSO-kapcsolatok megfelelő beállítása mindkét oldalon.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>ContractSafe-egy saml2 SSO-teszt felhasználó létrehozása

Hozzon létre egy B. Simon nevű felhasználót a ContractSafe egy saml2 SSO-ban. A [ContractSafe egy SAML2 SSO támogatási csapatával](mailto:support@contractsafe.com) a felhasználókat a CONTRACTSAFE egy saml2 SSO platformon veheti fel. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Tesztelje Azure AD SSO-konfigurációját a hozzáférési panel használatával. Amikor kiválasztja a ContractSafe egy saml2 SSO csempét a hozzáférési panelen, automatikusan be kell jelentkeznie a ContractSafe egy saml2 SSO-ba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Próbálja ki a ContractSafe egy saml2 SSO-t az Azure AD-vel](https://aad.portal.azure.com/)