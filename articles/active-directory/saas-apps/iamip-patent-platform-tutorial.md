---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a IamIP Patent platformmal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a IamIP-alapú szabadalmi platform között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.openlocfilehash: dc9be41cd7975610f2779da09b94066c70a91652
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460529"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a IamIP szabadalmi platformmal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a IamIP szabadalmi platformot Azure Active Directory (Azure AD) használatával. Ha integrálja a IamIP szabadalmi platformot az Azure AD-vel, a következőket teheti:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a IamIP szabadalmi platformhoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával, hogy IamIP a szabadalmi platformot.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg az [egyszeri bejelentkezést a Azure Active Directory alkalmazásaihoz](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezést (SSO) engedélyező IamIP-platformos előfizetés.

## <a name="tutorial-description"></a>Oktatóanyag leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését tesztkörnyezetben végezheti el.

A IamIP szabadalmi platform támogatja az SP által kezdeményezett és a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést.

A IamIP szabadalmi platform konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).


## <a name="add-iamip-patent-platform-from-the-gallery"></a>IamIP szabadalmi platform hozzáadása a katalógusból

A IamIP szabadalmi platform Azure AD-be való integrálásának konfigurálásához hozzá kell adnia egy IamIP-szabadalmi platformot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók.
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **IamIP szabadalmi platform** kifejezést a keresőmezőbe.
1. Válassza ki a **IamIP szabadalmi platformot** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Az Azure AD SSO konfigurálása és tesztelése a IamIP szabadalmi platformon

Az Azure AD SSO-t a IamIP Patent platformmal konfigurálhatja és tesztelheti egy B. Simon nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a megfelelő felhasználó között a IamIP szabadalmi platformon.

Az Azure AD SSO az IamIP Patent platformmal való konfigurálásához és teszteléséhez a következő magas szintű lépéseket kell végrehajtania:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t annak engedélyezéséhez, hogy a felhasználók használják a szolgáltatást.
    * **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez.
    * **[Hozzáférés biztosítása a tesztelési felhasználónak](#grant-access-to-the-test-user)** , hogy lehetővé váljon a felhasználó számára az Azure ad egyszeri bejelentkezés használata.

1. **[Konfigurálja a IamIP szabadalmi platform SSO](#configure-iamip-patent-platform-sso)** -t az alkalmazás oldalán.
    * **[Hozzon létre egy IamIP szabadalmas platformot](#create-iamip-patent-platform-test-user)** , amely a felhasználó Azure ad-beli képviseletének megfelelő felhasználói felülettel rendelkezik.

1. Ellenőrizze az **[SSO](#test-sso)** -t a konfiguráció működésének ellenőrzéséhez.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO-t a Azure Portalban:

1. A [Azure Portal](https://portal.azure.com/)a **IamIP szabadalmi platform** alkalmazás-integráció lapján, a **kezelés** szakaszban válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza gombot a beállítások szerkesztéséhez:

   ![Az alapszintű SAML-konfiguráció ceruza gombja](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha rendelkezik egy szolgáltatói metaadat-fájllal, és az egyszeri bejelentkezést identitásszolgáltató módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. Válassza a **metaadatok feltöltése** elemet:

    ![Metaadat-fájl feltöltése](common/upload-metadata.png)

    b. Kattintson a mappa gombra, válassza ki a metaadat-fájlt, majd válassza a **feltöltés** lehetőséget:

    ![Mappa és feltöltés gomb](common/browse-upload-metadata.png)

    c. A metaadatok feltöltése után az **azonosító** és a **Válasz URL-** értékei automatikusan betöltődik az **alapszintű SAML-konfiguráció** szakaszba:

    ![Azonosító és válasz URL-értékei](common/idp-intiated.png)

    > [!Note]
    > Ha az **azonosító** és a **Válasz URL-címe** nem töltődik fel automatikusan, az értékeket az igényeinek megfelelően adja meg manuálisan.

1. Válassza a **további URL-címek beállítása** lehetőséget, majd hajtsa végre a következő lépést, ha az alkalmazást SP-kezdeményezésű módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** mezőbe írja be a  **https: \/ /Patents.iamip.com/login-User** értéket.

1. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza ki a tanúsítvány **(RAW)** **letöltési** hivatkozását a tanúsítvány letöltéséhez, és mentse azt a számítógépre:

    ![Tanúsítvány letöltési hivatkozása](common/certificateraw.png)

1. A **IamIP szabadalmi platform beállítása** szakaszban a követelmények alapján másolja a megfelelő URL-címet vagy URL-címeket:

    ![Konfigurációs URL-címek másolása](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget. Válassza a **felhasználók** lehetőséget, majd válassza **a minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre a következő lépéseket:
   1. A név mezőbe írja be a **B. Simon** **nevet** .  
   1. A **Felhasználónév** mezőbe írja be a nevet \<username> @ \<companydomain> . \<extension> Például: `B.Simon@contoso.com`.
   1. Válassza a **jelszó megjelenítése** lehetőséget, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="grant-access-to-the-test-user"></a>Hozzáférés biztosítása a tesztelési felhasználónak

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy a felhasználó hozzáférést biztosít a IamIP szabadalmi platformhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza ki a **IamIP szabadalmi platform** elemet.
1. Az alkalmazás Áttekintés lapjának **kezelés** szakaszában válassza a **felhasználók és csoportok** lehetőséget:

   ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget:

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza az **B. Simon** elemet a **felhasználók** listán, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-iamip-patent-platform-sso"></a>A IamIP szabadalmi platform egyszeri bejelentkezésének konfigurálása

Ha az egyszeri bejelentkezést a IamIP Patent platform oldalon szeretné konfigurálni, el kell küldenie a letöltött nyers tanúsítványt és a Azure Portalról a [IamIP szabadalmi platform támogatási csapatához](mailto:info@iamip.com)másolt megfelelő URL-címeket. Az SAML SSO-kapcsolatokat úgy konfigurálja, hogy mindkét oldalon megfelelő legyen.

### <a name="create-iamip-patent-platform-test-user"></a>IamIP szabadalmi platform tesztelési felhasználó létrehozása

Működjön együtt a [IamIP szabadalmi platform támogatási csapatával](mailto:info@iamip.com) , és vegyen fel egy B. Simon nevű felhasználót a IamIP szabadalmi platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD SSO konfigurációját a hozzáférési panel használatával tesztelheti.

Amikor kiválasztja a IamIP szabadalmi platform csempét a hozzáférési panelen, automatikusan be kell jelentkeznie a IamIP szabadalmi platform-példányba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelbe](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Útmutatók az SaaS-alkalmazások Azure Active Directory-nal való integrálásához](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)

- [Próbálja ki a IamIP szabadalmi platformot az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)