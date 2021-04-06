---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása sPlan-látogatóval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a sPlan-látogató között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/14/2020
ms.author: jeedes
ms.openlocfilehash: 20f49c174dde90bc7f1a9b34f3dea3132e9b177e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644697"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása sPlan-látogatóval

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a sPlan-látogatókat Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a sPlan-látogatót, a következőket teheti:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a sPlan-látogatóhoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával való sPlan-látogatóba.
* A fiókokat egy központi helyen, a Azure Portal kezelheti.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SPlan-látogatói egyszeri bejelentkezéses (SSO) előfizetés engedélyezve.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését tesztkörnyezetben végezheti el.

A sPlan-látogató támogatja a identitásszolgáltató által kezdeményezett egyszeri bejelentkezést.

## <a name="add-splan-visitor-from-the-gallery"></a>SPlan-látogató hozzáadása a katalógusból

A sPlan-látogató Azure AD-ba való integrálásának konfigurálásához adja hozzá a sPlan-látogatót a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban adja meg a **sPlan látogató** kifejezést a keresőmezőbe.
1. Válassza ki a **sPlan látogatót** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>Azure AD SSO konfigurálása és tesztelése sPlan-látogató számára

Konfigurálja és tesztelje az Azure AD SSO-t a sPlan-látogatóval egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a sPlan-látogatóban.

Az Azure AD SSO sPlan-látogatóval való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon-felhasználóval.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Konfigurálja az sPlan-látogatói SSO](#configure-splan-visitor-sso)** -t az egyszeri bejelentkezési beállítások konfigurálásához a sPlan-látogatóval.
    1. **[Hozzon létre egy sPlan-látogatói tesztelési felhasználót](#create-a-splan-visitor-test-user)** , hogy a sPlan-látogatóban található B. Simon párja legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO-t a Azure Portalban:

1. A Azure Portal a **sPlan látogatói** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza a **Szerkesztés/toll** ikont az **alapszintű SAML-konfigurációhoz** a beállítások szerkesztéséhez.

   ![Képernyőkép a Szerkesztés/toll ikon kiemeléséről az alapszintű SAML-konfigurációhoz.](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban az alkalmazás előre konfigurálva van, a szükséges URL-címek pedig az Azure-ban vannak feltöltve. A konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra.

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját**. A **Letöltés** gombra kattintva letöltheti a tanúsítványt, és mentheti a számítógépre.

    ![Képernyőfelvétel: az összevonási metaadatok XML letöltési hivatkozása.](common/metadataxml.png)

1. A **sPlan-látogató beállítása** szakaszban másolja ki a megfelelő URL-címet vagy URL-címeket a követelmény alapján.

    ![Képernyőfelvétel: a konfigurációs URL-címek szakasza.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A név mezőbe írja be a **B. Simon** **nevet** .  
   1. A Felhasználónév **mezőben adja meg a** felhasználónevét _username@companydomain.extension_ formátumban. Adja meg például a következőt: **B.Simon@contoso.com** .
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a sPlan látogatói számára.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listájában válassza a **sPlan-látogató** lehetőséget az alkalmazás áttekintésének megnyitásához.
1. Keresse meg a **kezelés** szakaszt, majd válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a **felhasználók** listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználó hozzá lesz rendelve egy szerepkörhöz, válassza ki a **szerepkör kiválasztása** legördülő menüből. Ha nem állított be szerepkört ehhez az alkalmazáshoz, hagyja kiválasztva az **alapértelmezett hozzáférési** szerepkört.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-splan-visitor-sso"></a>SPlan-látogató egyszeri bejelentkezésének konfigurálása

Ha az egyszeri bejelentkezést a sPlan-látogatóval szeretné konfigurálni, küldje el a letöltött **összevonási metaadatok XML** -jét, valamint a Azure Portal a [sPlan látogatói támogatási csapatának](mailto:support@splan.com)megfelelő másolt URL-címeket. Ez biztosítja, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

### <a name="create-a-splan-visitor-test-user"></a>SPlan-látogató tesztelési felhasználó létrehozása

Hozzon létre egy **Britta Simon** nevű teszt felhasználót a sPlan-látogatóban. Működjön együtt a [sPlan látogatói támogatási csapatával](mailto:support@splan.com) , és vegye fel a felhasználót a sPlan-látogatóba. Az egyszeri bejelentkezés használata előtt létre kell hoznia és aktiválnia kell a felhasználót.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Tesztelje az Azure AD egyszeri bejelentkezési konfigurációját az alábbi lehetőségek egyikével:

* **Azure Portal**: válassza az **alkalmazás tesztelése** lehetőséget, hogy automatikusan bejelentkezzen arra a sPlan-látogatóba, amelyhez be szeretné állítani az egyszeri bejelentkezést.
* **Microsoft My apps portál**: válassza a **sPlan-látogató** csempét, hogy automatikusan bejelentkezzen a sPlan-látogatóba, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a saját alkalmazások portálról: [Bejelentkezés és alkalmazások indítása a saját alkalmazások portálján](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

A sPlan-látogató konfigurálása után [megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlőket a Microsoft Cloud app Securityban](/cloud-app-security/proxy-deployment-any-app). A munkamenet-vezérlők valós időben védik a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből.