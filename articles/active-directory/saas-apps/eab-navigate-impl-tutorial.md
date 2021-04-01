---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Emődi navigáló IMPL | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Emődi között a IMPL.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: a3f1982e72fb572f75789f6005b32de557c0b37d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454479"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eab-navigate-impl"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Emődi navigáló IMPL

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Emődi navigáló IMPL Azure Active Directory (Azure AD-val). Ha integrálja a Emődi a IMPL az Azure AD-vel, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Emődi navigáló IMPL.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek, hogy Emődi a IMPL az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Emődi a IMPL egyszeri bejelentkezés (SSO) engedélyezett előfizetését navigálja.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Emődi – a IMPL támogatja az **SP** által KEZDEMÉNYEZett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-eab-navigate-impl-from-the-gallery"></a>Emődi hozzáadása a IMPL a katalógusból

A Emődi az Azure AD-be történő IMPL-integrációjának konfigurálásához hozzá kell adnia a Emődi-IMPL a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Emődi a IMPL navigálása** kifejezést a keresőmezőbe.
1. Válassza a **Emődi navigáljon IMPL** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-eab-navigate-impl"></a>Azure AD-beli egyszeri bejelentkezés konfigurálása és tesztelése a Emődi navigálása IMPL

Konfigurálja és tesztelje az Azure AD SSO-t a Emődi-IMPL a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Emődi navigálása IMPL.

Az Azure AD SSO konfigurálásához és teszteléséhez a Emődi navigáló IMPL hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Emődi konfigurálása a IMPL egyszeri bejelentkezéshez](#configure-eab-navigate-impl-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Emődi-alapú IMPL-tesztelési felhasználó létrehozása](#create-eab-navigate-impl-test-user)** – a Emődi-hez tartozó B. Simon-hez tartozó IMPL navigáljon, amely a felhasználó Azure ad-beli képviseletéhez van csatolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Emődi navigáljon a IMPL** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit: az **azonosító (Entity ID)** szövegmezőben adja meg pontosan a következő értéket:  `https://impl.bouncer.eab.com`
    
    A **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** szövegmezőben adja meg a következő értékeket is külön sorként: `https://impl.bouncer.eab.com/sso/saml2/acs`
    `https://impl.bouncer.eab.com/sso/saml2/acs/`
    
    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<SUBDOMAIN>.navigate.impl.eab.com/`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a [Emődi a IMPL ügyfél-támogatási csapatával](mailto:EABTechSupport@eab.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Emődi navigáló IMPL.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Emődi navigáljon IMPL** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-eab-navigate-impl-sso"></a>Emődi-böngészés konfigurálása IMPL SSO

Ha be szeretné állítani az egyszeri bejelentkezést a **Emődi, navigáljon a IMPL** oldalára, el kell küldenie az **alkalmazás-összevonási metaadatok URL-címét** a [Emődi-IMPL támogatási csapatának](mailto:EABTechSupport@eab.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-eab-navigate-impl-test-user"></a>Emődi-navigáló IMPL-tesztelési felhasználó létrehozása

Ebben a szakaszban létrehoz egy B. Simon nevű felhasználót a Emődi navigál IMPL. A Emődi használata a [IMPL támogatási csapatával](mailto:EABTechSupport@eab.com) a felhasználók hozzáadásához a Emődi navigáljon IMPL platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Emődi navigál IMPL csempére kattint, automatikusan be kell jelentkeznie a Emődi navigáló IMPL, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Próbálja ki a Emődi a IMPL az Azure AD-vel](https://aad.portal.azure.com/)