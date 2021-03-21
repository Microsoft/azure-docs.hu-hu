---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Azure-hoz készült NS1 SSO-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a NS1 SSO között az Azure-ban.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.openlocfilehash: a7fad0af26d15bc77a52d55309c594937ba3be13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518619"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Azure-hoz készült NS1 SSO-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a NS1 SSO-t az Azure-ba a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja az NS1 SSO-t az Azure-ba, a következőket teheti:

* Az Azure AD-ben elérhető NS1 SSO-hozzáférés az Azure-ban.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezzenek az Azure-ba az Azure AD-fiókjával való NS1 egyszeri bejelentkezésre.
* A fiókokat egy központi helyen, a Azure Portal kezelheti.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory](../manage-apps/what-is-single-sign-on.md)használatával című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* NS1 SSO az Azure egyszeri bejelentkezés (SSO) számára engedélyezett előfizetés esetén.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A NS1 SSO for Azure az SP és a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést támogatja.
* Miután konfigurálta a NS1 SSO-t az Azure-hoz, kényszerítheti a munkamenet-vezérlést. Ez valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>NS1 SSO hozzáadása az Azure-hoz a katalógusból

Az Azure NS1 SSO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Azure-hoz készült NS1 SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe a **ns1 egyszeri bejelentkezés az Azure** -ban kifejezést.
1. Válassza az **ns1 SSO az Azure** -hoz lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az Azure-hoz készült NS1 SSO-hoz

Konfigurálja és tesztelje az Azure AD SSO-t az Azure-hoz készült NS1 SSO-vel egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez hozzon létre egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az Azure NS1 SSO-hoz.

Az Azure AD SSO konfigurálásának és tesztelésének általános lépései az Azure-hoz készült NS1 SSO használatával:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.

    a. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.

    b. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Konfigurálja a ns1 SSO-t az Azure SSO](#configure-ns1-sso-for-azure-sso)** -hoz az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.

    a. **[Hozzon létre egy ns1 SSO-t az Azure test User](#create-an-ns1-sso-for-azure-test-user)** számára, hogy a B. Simon párja legyen a ns1 SSO for Azure-ban. Ez a partner a felhasználó Azure AD-képviseletéhez van társítva.
1. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **ns1 SSO for Azure** Application Integration oldalon keresse meg a **kezelés** szakaszt. Válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

   ![Képernyőfelvétel: egyszeri bejelentkezés beállítása SAML-oldallal – kiemelve a ceruza ikonnal](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be a következő URL-címet: `https://api.nsone.net/saml/metadata`

    b. A **Válasz URL-cím** szövegmezőbe írja be a következő mintát használó URL-címet: `https://api.nsone.net/saml/sso/<ssoid>`

1. Válassza a **további URL-címek beállítása** lehetőséget, majd hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be a következő URL-címet:  `https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. A válasz URL-címének frissítése a tényleges válasz URL-címével. Az érték beszerzéséhez lépjen kapcsolatba a ns1 SSO-val az [Azure ügyfél-támogatási csapatával](mailto:techops@nsone.net) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az Azure-alkalmazásokhoz készült NS1 egyszeri bejelentkezés egy adott formátumban vár az SAML-kijelentésekre. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit a **felhasználói attribútumok & jogcímek** szakaszban találja az alkalmazás-integráció lapon. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza a ceruza ikont a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![Képernyőkép a felhasználói attribútumok & a jogcímek szakaszról, a ceruza ikon kiemelve](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Válassza ki az attribútum nevét a jogcím szerkesztéséhez.

    ![Képernyőkép a felhasználói attribútumok & a jogcímek szakaszról, az attribútum neve kiemelve](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Válassza az **átalakítás** lehetőséget.

    ![Képernyőkép a jogcím kezelése szakaszról, az átalakítás kiemelve](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. Az **átalakítás kezelése** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőkép – az átalakítás kezelése szakasz, különböző mezők kiemelésével](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Válassza a **ExactMailPrefix ()** lehetőséget **átalakításként**.

    1. Válassza a **User. userPrincipalName** elemet az **1. paraméterként**.

    1. Válassza a **Hozzáadás** lehetőséget.

    1. Kattintson a **Mentés** gombra.

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban válassza a másolás gombot. Ekkor a rendszer átmásolja az **alkalmazás-összevonási metaadatok URL-címét** , és menti azt a számítógépre.

    ![Képernyőfelvétel az SAML-aláíró tanúsítványról, a másolás gombbal kiemelve](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:

   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban az Azure egyszeri bejelentkezés használatát teszi lehetővé a B. Simon számára, hogy hozzáférést biztosítson az Azure-hoz készült NS1 SSO-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **ns1 egyszeri bejelentkezés az Azure**-hoz lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![Képernyőfelvétel a kezelés szakaszról, Kiemelt felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza a **Felhasználó hozzáadása** elemet. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![Képernyőkép a felhasználók és csoportok oldalról, a felhasználó hozzáadása elem kiemelésével](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza ki az **B. Simon** elemet a felhasználók listából. Ezután kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Ezután kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-ns1-sso-for-azure-sso"></a>NS1 SSO konfigurálása az Azure SSO-hoz

Ha az egyszeri bejelentkezést az Azure NS1 SSO-ra szeretné konfigurálni, akkor az alkalmazás-összevonási metaadatok URL-címét el kell küldenie az [Azure támogatási csapatának ns1 egyszeri bejelentkezéshez](mailto:techops@nsone.net). Ezt a beállítást úgy konfigurálja, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>NS1 SSO létrehozása az Azure test userhez

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre az Azure-beli NS1 SSO-hoz. Működjön együtt a NS1 SSO for Azure támogatási csapatával, és adja hozzá a felhasználókat a NS1 SSO for Azure platformhoz. Nem használhat egyszeri bejelentkezést, amíg nem hoz létre és nem aktivál felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával tesztelheti.

Amikor kiválasztja a NS1 SSO for Azure csempét a hozzáférési panelen, automatikusan be kell jelentkeznie az Azure-hoz készült NS1 SSO-ra, amelyhez be kell állítania az SSO-t. További információ: [Bevezetés a hozzáférési panelbe](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Az Azure AD-vel való NS1 egyszeri bejelentkezés kipróbálása](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)