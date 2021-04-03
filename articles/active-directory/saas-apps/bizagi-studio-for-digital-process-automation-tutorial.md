---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a digitális folyamatok automatizálására szolgáló bizarr | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a digitális folyamatok automatizálása Azure Active Directory és bizarr között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.openlocfilehash: efbb8a9ca0d475939d7713fa6a6a4a8245aead90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92457061"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-for-digital-process-automation"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a digitális folyamatok automatizálására szolgáló bizarr

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a bizarr a digitális folyamatok automatizálására szolgáló szolgáltatásokhoz vagy kiszolgálókhoz Azure Active Directory (Azure AD) használatával. Ha integrálja a bizarr-t a digitális folyamatok automatizálására az Azure AD-vel, a következőket teheti:

* A digitális folyamatok automatizálási szolgáltatásaihoz vagy kiszolgálójához tartozó bizarr-projekthez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek egy bizarr-projektbe a digitális folyamat AutomationServices vagy az Azure AD-fiókjával rendelkező kiszolgáló számára.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Automatizálási szolgáltatásokat vagy kiszolgálót használó bizarr-projekt. 
* Saját tanúsítványokkal rendelkezik az SAML-kijelentések aláírásához. A tanúsítványokat P12 vagy pfx formátumban kell előállítani.
* A bizarr-projektből létrehozott XML-formátumú metaadat-fájl található. 

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését egy bizarr-projektben az Automation Services vagy a Server használatával végezheti el.

* A bizarr for digitális Process Automation támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* Miután konfigurálta a bizarr a digitális folyamatok automatizálásához, kikényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-bizagi-for-digital-process-automation-from-the-gallery"></a>Bizarr hozzáadása a digitális folyamatok automatizálásához a katalógusból

A digitális folyamatok automatizálásának az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a digitális folyamatok automatizálásához szükséges bizarr a bizarr a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **bizarr a digitális folyamatok automatizálásához** kifejezést a keresőmezőbe.
1. Válassza a **bizarr lehetőséget a digitális folyamatok automatizálásához** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-bizagi-for-digital-process-automation"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a digitális folyamatok automatizálására szolgáló bizarr

Konfigurálja és tesztelje az Azure AD SSO-t a bizarr a digitális folyamatok automatizálásához egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a bizarr projektben.

Az Azure AD SSO és a digitális folyamatok automatizálásának bizarr való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[bizarr konfigurálása a digitális folyamatok automatizálására – SSO](#configure-bizagi-for-digital-process-automation-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Bizarr létrehozása a digitális folyamat-automatizálási teszt felhasználója](#create-bizagi-for-digital-process-automation-test-user)** számára – ha a felhasználó Azure ad-képviseletéhez kapcsolódó digitális folyamatok automatizálásához a bizarr-hez tartozó B. Simon-ügyféllel rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **digitális folyamatok automatizálása bizarr** oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Töltse fel a bizarr metaadat-fájlját a **metaadatok feltöltése** lehetőségbe.
1. Tekintse át a konfigurációt. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be a bizarr-projekt URL-címét: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be a bizarr-projekt URL-címét: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Vegye fel a kapcsolatot [a bizarr a digitális folyamatok automatizálását támogató csapattal](mailto:jarvein.rivera@bizagi.com) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)
    
    Ezt a metaadat-URL-címet regisztrálni kell a bizarr-projekt hitelesítési beállításaiban.
    
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a felhasználói attribútumok szerkesztés/toll ikonjára **& jogcímek** elemre az egyedi felhasználói azonosító szerkesztéséhez.
    
    Állítsa be az egyedi felhasználói azonosítót a User. mail néven.

### <a name="create-an-azure-ad-test"></a>Azure AD-teszt létrehozása 

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a bizarr a digitális folyamatok automatizálásához.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a bizarr lehetőséget a **digitális folyamatok automatizálásához**.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-bizagi-for-digital-process-automation-sso"></a>A bizarr konfigurálása a digitális folyamatok automatizálásának egyszeri bejelentkezéséhez

A **digitális folyamatok automatizálásának bizarr** való egyszeri bejelentkezés konfigurálásához el kell küldenie az **alkalmazás-összevonási metaadatok URL-címét** a [bizarr for Digital Process Automation támogatási csapatának](mailto:jarvein.rivera@bizagi.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-bizagi-for-digital-process-automation-test-user"></a>Bizarr létrehozása a digitális folyamatok automatizálásának tesztelésére szolgáló felhasználó számára

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a bizarr a digitális folyamatok automatizálásához. A bizarr használata a [digitális folyamatok automatizálását támogató csapat](mailto:jarvein.rivera@bizagi.com) számára a bizarr a digitális folyamatok automatizálására szolgáló platformhoz való hozzáadásához. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a digitális folyamatok automatizálása csempéhez bizarr kattint, a rendszer automatikusan bejelentkezik a bizarr for digitális Process Automation portálra, amelyhez be van állítva az egyszeri bejelentkezés. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Próbálja ki a digitális folyamatok automatizálásának bizarr az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)