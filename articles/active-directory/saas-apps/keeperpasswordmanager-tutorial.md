---
title: 'Oktatóanyag: Azure Active Directory integráció a Keeper Password Managerrel & Digital Vaulttal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Keeper Password Manager & Digital Vault között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: 88f84fba43959ee5e5b8d93446e4985a75697813
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685868"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Oktatóanyag: Azure Active Directory integráció a Keeper Password Managerrel & Digital Vaulttal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Keeper Password Managert & Digital Vaultba Azure Active Directory (Azure AD).
A Keeper Password Manager & Digital Vault és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Keeper Password Manager & Digital Vaulthoz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy az Azure AD-fiókjával & digitális tárolóban (egyszeri bejelentkezés) megőrizze a Password Managert.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.


## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt a Keeper Password Managerrel & Digital Vaulttal szeretné konfigurálni, a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* A Keeper Password Manager & a digitális tár egyszeri bejelentkezésre engedélyezett előfizetését

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Keeper Password Manager & Digital Vault támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A Keeper Password Manager & Digital Vault **a** felhasználók üzembe helyezését támogatja

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>A Keeper Password Manager & a digitális tár hozzáadása a katalógusból

A Keeper Password Manager & Digital Vault Azure AD-ba való integrálásának konfigurálásához a Keeper Password Managert & Digital Vaultot a katalógusból a felügyelt SaaS-alkalmazások listájára kell felvennie.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Keeper Password Manager & Digital Vault** kifejezést a keresőmezőbe.
1. Válassza a **Keeper Password Manager & Digital Vault** az eredmények panelen, majd az alkalmazás hozzáadása lehetőséget. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Azure AD SSO konfigurálása és tesztelése a Keeper Password Manager & Digital Vaulthoz

Konfigurálja és tesztelje az Azure AD SSO-t a Keeper Password Managerrel & Digital Vaultot egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és az érintett felhasználó között a Keeper Password Manager & Digital Vaultban.

Az Azure AD SSO és a Keeper Password Manager & Digital Vault konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.

    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    * **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.

1. A **[Keeper Password Manager & digitális tároló egyszeri bejelentkezésének konfigurálása](#configure-keeper-password-manager--digital-vault-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    * A **[Keeper Password manager & a digitális tár tesztelési felhasználójának létrehozása](#create-keeper-password-manager--digital-vault-test-user)** – hogy a Britta egy, a felhasználó Azure ad-képviseletéhez kapcsolódó, a Keeper Password Managerben található, a (& Digitális tárolóban található).
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Keeper Password Manager & Digital Vault** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:
    * **Felhőalapú egyszeri bejelentkezés** esetén:`https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * **Helyszíni SSO** esetén:`https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:
    * **Felhőalapú egyszeri bejelentkezés** esetén:`https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * **Helyszíni SSO** esetén:`https://<KEEPER_FQDN>/sso-connect`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:
    * **Felhőalapú egyszeri bejelentkezés** esetén:`https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * **Helyszíni SSO** esetén:`https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Vegye fel a kapcsolatot a [Keeper Password managerrel & a digitális tároló ügyfél-támogatási csapatával](https://keepersecurity.com/contact.html) , hogy ezeket az értékeket kapja. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Keeper Password Manager & Digital Vault-alkalmazás megadott formátumban várja az SAML-jogcímeket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Keeper Password Manager & Digital Vault alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Név | Forrás attribútum|
    | ------------| --------- |
    | Első | User. givenName |
    | Utolsó | felhasználó. vezetéknév |
    | E-mail | User. mail |

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Keeper Password Manager beállítása & digitális** tároló szakaszban másolja ki a megfelelő URL-címeket (ek) a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Keeper Password Manager & Digital Vaulthoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Keeper Password Manager & Digital Vault** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>A Keeper Password Manager konfigurálása a digitális tároló egyszeri bejelentkezésének &

Ha be szeretné állítani az egyszeri bejelentkezést a **Keeper Password managerben & a digitális tároló konfigurációs** oldalát, kövesse az [Keeper támogatási útmutatójában](https://docs.keeper.io/sso-connect-guide/)megadott útmutatást.

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>A Keeper Password Manager létrehozása & Digital Vault test User

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Keeper Password Manager & digitális tárolóba, ki kell építeni őket a Keepers Password Manager & Digital Vaultba. Az alkalmazás a felhasználó üzembe helyezését támogatja, és a hitelesítéssel rendelkező felhasználók automatikusan jönnek létre az alkalmazásban. Ha manuálisan szeretné beállítani a felhasználókat, vegye fel a kapcsolatot a [Keeper támogatási szolgálatával](https://keepersecurity.com/contact.html).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a Keeper Password Manager & a digitális tár bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen a Keeper Password Manager & Digital Vault bejelentkezési URL-címére közvetlenül, és onnan indítsa el a bejelentkezési folyamatot.

* Használhatja a Microsoft Access panelt. Ha a hozzáférés panelen a Keeper Password Manager & a digitális tároló csempére kattint, a rendszer átirányítja a Keeper Password Manager & a digitális tár bejelentkezési URL-címére. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a Keeper Password Managert & Digital Vaultot, kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)