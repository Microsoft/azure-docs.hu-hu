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
ms.openlocfilehash: b70c50e7c2900f884dd4d91c6650205bc626326e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96178041"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Oktatóanyag: Azure Active Directory integráció a Keeper Password Managerrel & Digital Vaulttal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Keeper Password Managert & Digital Vaultba Azure Active Directory (Azure AD).
Ez az integráció az alábbi előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Keeper Password Manager & Digital Vaulthoz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Keeper Password Manager & digitális tárolóba (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.


## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a Keeper Password Managerrel & Digital Vaulttal való konfigurálásához a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, egy [hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/)is beszerezhet.
* A Keeper Password Manager & a digitális tár előfizetését, amely engedélyezve van az egyszeri bejelentkezéshez (SSO).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Keeper Password Manager & Digital Vault támogatja az SP által kezdeményezett egyszeri bejelentkezést.

* A Keeper Password Manager & Digital Vault támogatja az igény szerinti felhasználói üzembe helyezést.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>A Keeper Password Manager & Digital Vault hozzáadása a katalógusból

A Keeper Password Manager & Digital Vault Azure AD-ba való integrálásának konfigurálásához adja hozzá az alkalmazást a katalógusból a felügyelt szolgáltatott szoftver (SaaS) alkalmazásai listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból mezőbe írja be a **Keeper Password Manager & Digital Vault** kifejezést a keresőmezőbe.
1. Válassza a **Keeper Password Manager & Digital Vault** az eredmények panelen, majd az alkalmazás hozzáadása lehetőséget. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Azure AD SSO konfigurálása és tesztelése a Keeper Password Manager & Digital Vaulthoz

Konfigurálja és tesztelje az Azure AD SSO-t a Keeper Password Managerrel & Digital Vaultot egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Keeper Password Manager & Digital Vaultban.

Az Azure AD SSO konfigurálása és tesztelése a Keeper Password Managerrel & Digital Vaulttal:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.

    * [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    * [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy a Britta Simon engedélyezze az Azure ad egyszeri bejelentkezés használatát.

1. [Konfigurálja a Keeper Password managert & Digital Vault SSO](#configure-keeper-password-manager--digital-vault-sso) -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    * [Hozzon létre egy megtartó jelszavas kezelőt & a digitális tár tesztelési felhasználójának](#create-a-keeper-password-manager--digital-vault-test-user) , hogy a felhasználó Azure ad-Britta kapcsolódó, a Keeper Password & Managerben található Simon-t használja.
1. Ellenőrizze az [SSO](#test-sso) -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Keeper Password Manager & Digital Vault** Application Integration oldalon keresse meg a **kezelés** szakaszt. Válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

   ![Képernyőfelvétel: egyszeres Sign-On beállítása SAML-vel, a ceruza ikon kiemelve.](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** mezőbe írja be a következő mintát használó URL-címet:
    * Felhőalapú egyszeri bejelentkezés esetén: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Helyszíni SSO esetén: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. Az **azonosító (entitás azonosítója)** mezőbe írjon be egy URL-címet, amely a következő mintát használja:
    * Felhőalapú egyszeri bejelentkezés esetén: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Helyszíni SSO esetén: `https://<KEEPER_FQDN>/sso-connect`

    c. A **Válasz URL-cím** mezőbe írja be a következő mintát használó URL-címet:
    * Felhőalapú egyszeri bejelentkezés esetén: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Helyszíni SSO esetén: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Ezeknek az értékeknek a beszerzéséhez lépjen kapcsolatba a [Keeper Password Manager & a digitális tároló ügyfél-támogatási csapatával](https://keepersecurity.com/contact.html). Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Keeper Password Manager & Digital Vault-alkalmazás megadott formátumban várja az SAML-jogcímeket, és ehhez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Képernyőkép & jogcímek felhasználói attribútumairól.](common/default-attributes.png)

1. Emellett a Keeper Password Manager & Digital Vault alkalmazás néhány további attribútumot vár az SAML-válaszban való visszatéréshez. Ezek az alábbi táblázatban láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelmények szerint áttekinthetők.

    | Name | Forrás attribútum|
    | ------------| --------- |
    | Első | User. givenName |
    | Utolsó | felhasználó. vezetéknév |
    | E-mail | User. mail |

5. Az **egyszeri Sign-On SAML használatával történő beállításához** az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget. Ez letölti az **összevonási metaadatok XML-** fájlját a szükséges beállítások alapján, és elmenti a számítógépre.

    ![Képernyőkép az SAML-aláíró tanúsítványról, a letöltés kiemelve.](common/metadataxml.png)

6. A **Keeper Password Manager & digitális tároló beállítása** lapon másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Képernyőkép a Keeper Password Manager & Digital Vault beállításáról, amely kiemelt URL-címekkel rendelkezik.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal néven `B.Simon` .

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.
1. A képernyő felső részén válassza az **új felhasználó** lehetőséget.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **név** mezőbe írja be a következőt: `B.Simon` .  
   1. A **Felhasználónév** mezőbe írja be a következőt: `username@companydomain.extension` . Például: `B.Simon@contoso.com`.
   1. Válassza a **jelszó megjelenítése** lehetőséget, majd írja le a megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezi a B. Simon számára az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a megtartó Password Managerhez & Digital Vaulthoz.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Keeper Password Manager & Digital Vault** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **Felhasználó hozzáadása** elemet. A **Hozzárendelés felvétele** területen válassza a **Felhasználók és csoportok** elemet.
1. A **felhasználók és csoportok** területen válassza ki a felhasználók listájából a **B. Simon** elemet. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, akkor az **alapértelmezett hozzáférési** szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** lapon válassza a **hozzárendelés** lehetőséget.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>A Keeper Password Manager konfigurálása a digitális tároló egyszeri bejelentkezésének &

Az SSO az alkalmazáshoz való konfigurálásához tekintse meg a [Keeper támogatási útmutatójának](https://docs.keeper.io/sso-connect-guide/)útmutatásait.

### <a name="create-a-keeper-password-manager--digital-vault-test-user"></a>A Keeper Password Manager & a digitális tár tesztelési felhasználójának létrehozása

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezzenek a Keeper Password Manager & digitális tárolóba, ki kell építenie azokat. Az alkalmazás támogatja az igény szerinti felhasználói üzembe helyezést, és a hitelesítéssel rendelkező felhasználók automatikusan jönnek létre az alkalmazásban. Ha manuálisan szeretné beállítani a felhasználókat, vegye fel a kapcsolatot a [Keeper támogatási szolgálatával](https://keepersecurity.com/contact.html).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* A Azure Portal válassza az **alkalmazás tesztelése** lehetőséget. Ez átirányítja a Keeper Password Manager & digitális tároló bejelentkezési URL-címére, ahol elindíthatja a bejelentkezést. 

* Közvetlenül az alkalmazás bejelentkezési URL-címére léphet, és elindíthatja a bejelentkezést onnan.

* Használhatja a Microsoft Access panelt. Amikor kiválasztja a **Keeper Password Manager & a digitális** tárolót a hozzáférési panelen, a rendszer átirányítja az alkalmazás bejelentkezési URL-címére. További információ a hozzáférési panelről: [Bejelentkezés és alkalmazások indítása a saját alkalmazások portálján](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a Keeper Password Managert & Digital Vaultot, kényszerítheti a munkamenet-vezérlést. Ez valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. További információ: a [munkamenet-vezérlés kényszerített érvényesítése Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-aad).