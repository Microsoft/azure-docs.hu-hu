---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Skytap való egyszeri bejelentkezéssel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Skytap Azure Active Directory és az egyszeri bejelentkezés között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.openlocfilehash: 5577b0cb480b372b64a9cb25f52617edfca3f2e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98108160"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Skytap való egyszeri bejelentkezéssel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az egyszeri bejelentkezést a Skytap Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja az Skytap az egyszeri bejelentkezést, a következőket teheti:

* A Skytap való egyszeri bejelentkezéshez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az egyszeri bejelentkezésre az Azure AD-fiókjával való Skytap.
* A fiókokat egy központi helyen, a Azure Portal kezelheti.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory](../manage-apps/what-is-single-sign-on.md)használatával című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egyszeri bejelentkezés a Skytap egyszeri bejelentkezés (SSO) engedélyezett előfizetéséhez.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az egyszeri bejelentkezés a Skytap esetében támogatja az SP és a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést.
* Miután konfigurálta az egyszeri bejelentkezést a Skytap, kényszerítheti a munkamenet-vezérlést. Ez valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>Egyszeri bejelentkezés hozzáadása a Skytap a katalógusból

A Skytap egyszeri bejelentkezésének az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia egy egyszeri bejelentkezést a Skytap a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **egyszeri bejelentkezést a Skytap** .
1. Válassza az **egyszeri bejelentkezés** lehetőséget a Skytap az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Skytap való egyszeri bejelentkezéshez

Konfigurálja és tesztelje az Azure AD SSO-t egyszeri bejelentkezéssel a Skytap egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez hozzon létre egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az egyszeri bejelentkezésben a Skytap.

Az alábbi általános lépésekkel konfigurálhatja és tesztelheti az Azure AD SSO-t egyszeri bejelentkezéssel a Skytap-hez:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.

    a. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.

    b. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Konfigurálja az egyszeri bejelentkezést a **[SKYTAP egyszeri](#configure-single-sign-on-for-skytap-sso)** bejelentkezéshez az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.

    a. **[Hozzon létre egy egyszeri bejelentkezést a Skytap-tesztelési felhasználó](#create-single-sign-on-for-skytap-test-user)** számára, hogy a B. Simon párja legyen a Skytap-beli egyszeri bejelentkezésben. Ez a partner a felhasználó Azure AD-képviseletéhez van társítva.
1. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a Skytap-alkalmazás integrációjának **egyszeri bejelentkezés** lapján keresse meg a **kezelés** szakaszt. Válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

   ![Képernyőfelvétel: egyszeri bejelentkezés beállítása SAML-oldallal – kiemelve a ceruza ikonnal](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be a következő mintát használó URL-címet: `http://pingone.com/<custom EntityID>`

    b. A **Válasz URL-cím** szövegmezőbe írja be a következő mintát használó URL-címet: `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Kiválaszthatja a **további URL-címek beállítása** lehetőséget is, és a következő lépésekkel konfigurálhatja az alkalmazást **SP** -kezdeményezésű módban:

    a. A **bejelentkezési URL** szövegmezőbe írja be a következő mintát használó URL-címet: `https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. A **továbbítási állapot** szövegmezőbe írja be a következő mintát használó URL-címet: `https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével, a bejelentkezési URL-címmel és a továbbítási állapottal. Vegye fel a kapcsolatot az [egyszeri bejelentkezéssel a Skytap ügyfél-támogatási csapatának](mailto:support@skytap.com) az értékek lekéréséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját**. A **Letöltés** gombra kattintva letöltheti a metaadat-fájlt, és mentheti a számítógépre.

    ![A tanúsítvány letöltési hivatkozásának képernyőképe](common/metadataxml.png)

1. Az **egyszeri bejelentkezés beállítása Skytap** szakaszban másolja a megfelelő URL-címet vagy URL-címeket a követelmény alapján.

    ![A konfigurációs URL-címek másolásának képernyőképe](common/copy-configuration-urls.png)

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

Ebben a szakaszban engedélyezi, hogy a B. Simon az Azure egyszeri bejelentkezést használja a Skytap való egyszeri bejelentkezéshez való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Skytap egyszeri bejelentkezés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![Képernyőfelvétel a kezelés szakaszról, Kiemelt felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza a **Felhasználó hozzáadása** elemet. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![Képernyőkép a felhasználók és csoportok oldalról, a felhasználó hozzáadása elem kiemelésével](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza ki az **B. Simon** elemet a felhasználók listából. Ezután kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Ezután kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Egyszeri bejelentkezés konfigurálása a Skytap SSO-hoz

Ha az egyszeri bejelentkezést szeretné konfigurálni az Skytap oldalon, el kell küldenie a letöltött **összevonási metaadatokat tartalmazó XML-fájlt** és a megfelelő másolt URL-címeket a Azure Portalról a [Skytap-ügyfél támogatási csapatának egyszeri bejelentkezéséhez](mailto:support@skytap.com). Ezt a beállítást úgy konfigurálja, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Egyszeri bejelentkezés létrehozása a Skytap-tesztkörnyezet felhasználói számára

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre egyszeri bejelentkezéssel a Skytap számára. A [Skytap ügyfél-támogatási csapatának egyszeri bejelentkezésével](mailto:support@skytap.com) a felhasználókat a Skytap platform egyszeri bejelentkezésével veheti fel. Nem használhat egyszeri bejelentkezést, amíg nem hoz létre és nem aktivál felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával tesztelheti.

Ha a hozzáférési panelen bejelöli az Skytap csempe egyszeri bejelentkezését, akkor automatikusan be kell jelentkeznie a Skytap egyszeri Bejelentkezésbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ: [Bevezetés a hozzáférési panelbe](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Slack kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)
