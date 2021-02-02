---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Veracode | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Veracode között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 220a575a252bb16cb5ac895a3d4c1ebedfd7c83e
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99431035"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Veracode

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Veracode a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Veracode-t az Azure AD-vel, a következőket teheti:

* A Veracode-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Veracode az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Veracode egyszeri bejelentkezésre (SSO) alkalmas előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A Veracode támogatja a személyazonosság-szolgáltató által kezdeményezett egyszeri bejelentkezést és igény szerinti felhasználói üzembe helyezést.

## <a name="add-veracode-from-the-gallery"></a>Veracode hozzáadása a gyűjteményből

A Veracode Azure AD-be való integrálásának konfigurálásához adja hozzá a Veracode a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a "Veracode" kifejezést a keresőmezőbe.
1. Válassza az **Veracode** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-veracode"></a>Azure AD SSO konfigurálása és tesztelése a Veracode-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Veracode egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a Veracode-ben.

Az Azure AD SSO és a Veracode konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    * **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Konfigurálja a VERACODE SSO](#configure-veracode-sso)** -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    * **[Hozzon létre egy Veracode-tesztelési felhasználót](#create-veracode-test-user)** , hogy a Veracode B. Simon párja legyen a felhasználó Azure ad-képviseletéhez társítva.
1. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Veracode** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt. Válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban az alkalmazás előre konfigurálva van, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. Kattintson a **Mentés** gombra.

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** című szakaszt. A **Letöltés** gombra kattintva letöltheti a tanúsítványt, és mentheti a számítógépre.

    ![Képernyőfelvétel az SAML-aláíró tanúsítványról szakasz, a letöltési hivatkozás kiemelve](common/certificatebase64.png)

1. A Veracode egy adott formátumban várja az SAML-jogkivonatokat, amelyhez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Képernyőkép a felhasználói attribútumok & jogcímek szakaszról](common/default-attributes.png)

1. A Veracode Emellett néhány további attribútumot is vár az SAML-válaszban. Ezek az attribútumok előre fel vannak töltve, de a követelmények szerint áttekinthetők.

    | Name | Forrás attribútum|
    | ---------------| --------------- |
    | FirstName |User. givenName |
    | LastName |Felhasználó. vezetéknév |
    | e-mail |User. mail |

1. A **Veracode beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Képernyőkép a Veracode beállítása szakaszról, a konfigurációs URL-címek kiemelve](common/copy-configuration-urls.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Veracode.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Veracode** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-veracode-sso"></a>Veracode SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Veracode vállalati webhelyre rendszergazdaként.

1. A felső menüben válassza a **Beállítások**  >  **rendszergazda** elemet.
   
    ![Képernyőkép a Veracode felügyeletről, a beállítások ikon és a rendszergazda kiemelve](./media/veracode-tutorial/admin.png "Felügyelet")

1. Válassza ki az **SAML** lapot.

1. A **szervezet SAML-beállításai** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel a szervezeti SAML-beállításokról szakasz](./media/veracode-tutorial/saml.png "Felügyelet")

    a.  A **kiállító** esetében illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    b. Az **érvényesítési tanúsítványnál** válassza a **fájl kiválasztása** lehetőséget a letöltött tanúsítvány Azure Portal való feltöltéséhez.

    c. Az **Önregisztrációhoz** válassza a **saját regisztráció engedélyezése** lehetőséget.

1. Az **Önregisztráció beállításai** szakaszban hajtsa végre az alábbi lépéseket, majd válassza a **Mentés** lehetőséget:

    ![Képernyőfelvétel az Önregisztráció beállításairól szakasz, különböző lehetőségekkel](./media/veracode-tutorial/save.png "Felügyelet")

    a. Az **új felhasználói aktiváláshoz** válassza a **nincs szükség aktiválásra** lehetőséget.

    b. A **felhasználói adatfrissítések** esetében válassza a **preferencia Veracode felhasználói információk** lehetőséget.

    c. Az **SAML-attribútumok részleteihez** válassza ki a következőket:
      * **Felhasználói szerepkörök**
      * **Házirend rendszergazdája**
      * **Felülvizsgáló**
      * **Biztonsági érdeklődő**
      * **Vezető**
      * **Beküldő**
      * **Létrehozó**
      * **Minden vizsgálat típusa**
      * **Csoport tagsága**
      * **Alapértelmezett csapat**

### <a name="create-veracode-test-user"></a>Veracode-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Veracode-ben. A Veracode támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs művelet. Ha egy felhasználó még nem létezik a Veracode-ben, a rendszer egy újat hoz létre a hitelesítés után.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a Veracode által biztosított Veracode felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Veracode, amelyhez be szeretné állítani az egyszeri bejelentkezést.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások Veracode csempére kattint, automatikusan be kell jelentkeznie arra a Veracode, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

A Veracode konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
