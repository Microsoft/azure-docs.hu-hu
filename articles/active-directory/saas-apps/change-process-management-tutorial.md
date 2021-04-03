---
title: 'Oktatóanyag: az Azure AD SSO-integrációja a Change Process managementtel'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és módosíthatja a folyamatok felügyeletét.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: jeedes
ms.openlocfilehash: 03c78f05566876356e4f486368dc2a5b3a29de43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456258"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-change-process-management"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Change Process Management szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a változási folyamatokat a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a változási folyamatok felügyeletét, a következőket teheti:

* Az Azure AD használatával szabályozhatja, hogy ki férhet hozzá a Change Process Management szolgáltatáshoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a folyamatok Azure AD-fiókjával való kezelésének módosításához.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg az [egyszeri bejelentkezést a Azure Active Directory alkalmazásaihoz](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezéssel (SSO) rendelkező Change Process Management-előfizetés engedélyezve van.

## <a name="tutorial-description"></a>Oktatóanyag leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését tesztkörnyezetben végezheti el.

A Change Process Management támogatja a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést.

A módosítási folyamat kezelésének konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-change-process-management-from-the-gallery"></a>Módosítási folyamat kezelésének hozzáadása a katalógusból

A Change Process Management Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a gyűjtemény módosításait a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók.
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban adja meg a Research ( **folyamat kezelése** ) kifejezést a keresőmezőbe.
1. Válassza a **folyamat-kezelés módosítása** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-change-process-management"></a>Azure AD SSO konfigurálása és tesztelése a változási folyamatok kezeléséhez

Az Azure AD SSO konfigurálását és tesztelését egy B. Simon nevű teszt felhasználó használatával végezheti el a változási folyamatok kezelésével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a megfelelő felhasználó között a folyamat módosításainak kezelése területen.

Az Azure AD SSO konfigurálásához és teszteléséhez a Change Process Management használatával hajtsa végre a következő magas szintű lépéseket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t annak engedélyezéséhez, hogy a felhasználók használják a szolgáltatást.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez.
    1. **[Hozzáférés biztosítása a tesztelési felhasználónak](#grant-access-to-the-test-user)** , hogy lehetővé váljon a felhasználó számára az Azure ad egyszeri bejelentkezés használata.
1. A **[change Process Management SSO konfigurálása](#configure-change-process-management-sso)** az alkalmazás oldalán.
    1. **[Hozzon létre egy módosítási folyamat-felügyeleti teszt felhasználót](#create-a-change-process-management-test-user)** a felhasználó Azure ad-beli képviseletének megfelelő felhasználóként.
1. Ellenőrizze az **[SSO](#test-sso)** -t a konfiguráció működésének ellenőrzéséhez.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **folyamat kezelése** alkalmazás-integráció módosítása lap **kezelés** szakaszában válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza gombot a beállítások szerkesztéséhez:

   ![Az alapszintű SAML-konfiguráció ceruza gombja](common/edit-urls.png)

1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon hajtsa végre a következő lépéseket:

    a. Az **azonosító** mezőben adja meg az URL-címet a következő mintában: `https://<hostname>:8443/`

    b. A **Válasz URL-címe** mezőbe írja be az URL-címet a következő mintában: `https://<hostname>:8443/changepilot/saml/sso`

    > [!NOTE]
    > Az előző **azonosító** és a **Válasz URL-** értékei nem a ténylegesen használt értékek. A tényleges értékek lekéréséhez lépjen kapcsolatba a [change Process Management támogatási csapatával](mailto:support@realtech-us.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza ki a tanúsítvány **(Base64)** **letöltési** hivatkozását a tanúsítvány letöltéséhez, és mentse a számítógépre:

    ![Tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **módosítási folyamat kezelése** szakaszban másolja be a megfelelő URL-címet vagy URL-címeket a követelmények alapján:

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy megadja, hogy a felhasználó hozzáférést biztosít a folyamatok kezelésének módosításához.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **folyamat-kezelés módosítása** lehetőséget.
1. Az alkalmazás Áttekintés lapjának **kezelés** szakaszában válassza a **felhasználók és csoportok** lehetőséget:

   ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza az **B. Simon** elemet a **felhasználók** listán, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-change-process-management-sso"></a>A Change Process Management SSO konfigurálása

Ha az egyszeri bejelentkezést szeretné konfigurálni a folyamat módosításainak kezelése oldalon, el kell küldenie a letöltött Base64-tanúsítványt és a Azure Portalból átmásolt megfelelő URL-címeket a [change Process Management támogatási csapatának](mailto:support@realtech-us.com). Az SAML SSO-kapcsolatokat úgy konfigurálja, hogy mindkét oldalon megfelelő legyen.

### <a name="create-a-change-process-management-test-user"></a>Módosítási folyamat-felügyeleti teszt felhasználó létrehozása
 A [módosítási folyamat kezelése támogatási csapattal](mailto:support@realtech-us.com) együtt vegyen fel egy B. Simon nevű felhasználót a Change Process Management szolgáltatásban. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD SSO konfigurációját a hozzáférési panel használatával tesztelheti.

Amikor bejelöli a folyamat kezelése csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a Change Process Management-példányra, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelbe](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Útmutatók az SaaS-alkalmazások Azure Active Directory-nal való integrálásához](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)

- [Próbálja meg módosítani a folyamatok kezelését az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A változási folyamatok kezelése a speciális láthatósággal és ellenőrzésekkel](/cloud-app-security/proxy-intro-aad)