---
title: 'Oktatóanyag: Azure Active Directory-integráció a Veritas Enterprise Vault.cloud SSO-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Veritas Enterprise Vault.cloud SSO között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 2796928316197bd48723253dbc97dfcd34ac95e8
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222282"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Oktatóanyag: Azure Active Directory-integráció a Veritas Enterprise Vault.cloud SSO-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Veritas Enterprise Vault.cloud SSO-t Azure Active Directory (Azure AD-val). Ha a Veritas Enterprise Vault.cloud SSO-t az Azure AD-vel integrálja, a következőket teheti:

* A Veritas Enterprise Vault.cloud SSO-hoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Veritas Enterprise Vault.cloud SSO-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a Veritas Enterprise Vault.cloud SSO-val való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Veritas Enterprise Vault.cloud SSO egyszeri bejelentkezés engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Veritas Enterprise Vault.cloud SSO támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>A Veritas Enterprise Vault.cloud SSO hozzáadása a katalógusból

A Veritas Enterprise Vault.cloud SSO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Veritas Enterprise Vault.cloud SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Veritas Enterprise Vault.Cloud SSO** kifejezést a keresőmezőbe.
1. Válassza ki a **Veritas Enterprise Vault.Cloud SSO** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-veritas-enterprise-vaultcloud-sso"></a>Azure AD SSO konfigurálása és tesztelése a Veritas Enterprise Vault.cloud SSO-hoz

Az Azure AD SSO konfigurálása és tesztelése a Veritas Enterprise Vault.cloud SSO használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a Veritas Enterprise Vault.cloud SSO-ban.

Az Azure AD SSO és a Veritas Enterprise Vault.cloud SSO konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Veritas Enterprise Vault.Cloud SSO SSO konfigurálása](#configure-veritas-enterprise-vaultcloud-sso-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. A **[Veritas enterprise Vault.Cloud egyszeri bejelentkezéses tesztelési felhasználójának létrehozása](#create-veritas-enterprise-vaultcloud-sso-test-user)** – a felhasználó Azure ad-képviseletéhez kapcsolódó B
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Veritas Enterprise Vault.Cloud SSO** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. Az **azonosító** mezőbe írja be az egyik URL-címet az adatközpontként:

    | Adatközpont| URL-cím |
    |----------|----|
    | Észak-Amerika| `https://auth.lax.archivecloud.net` |
    | Európa | `https://auth.ams.archivecloud.net` |
    | Ázsia és a Csendes-óceáni térség| `https://auth.syd.archivecloud.net`|

    c. A **Válasz URL-címe** szövegmezőbe írja be az egyik URL-címet az adatközpontként:

    | Adatközpont| URL-cím |
    |----------|----|
    | Észak-Amerika| `https://auth.lax.archivecloud.net` |
    | Európa | `https://auth.ams.archivecloud.net` |
    | Ázsia és a Csendes-óceáni térség| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Ez az érték nem valós. Frissítse ezt az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez forduljon a [Veritas Enterprise Vault.Cloud SSO ügyfél-támogatási csapatához](https://www.veritas.com/support/.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Veritas Enterprise Vault.Cloud SSO beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)


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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a Veritas Enterprise Vault.cloud SSO hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Veritas Enterprise Vault.Cloud SSO** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-veritas-enterprise-vaultcloud-sso-sso"></a>A Veritas Enterprise Vault.cloud SSO SSO konfigurálása

Ha be szeretné állítani az egyszeri bejelentkezést a **Veritas enterprise Vault.Cloud SSO** oldalon, el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-címeket Azure Portalról a [Veritas Enterprise Vault.Cloud SSO támogatási csapatának](https://www.veritas.com/support/.html). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>A Veritas Enterprise Vault.cloud SSO-teszt felhasználójának létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Veritas Enterprise Vault.cloud SSO-ban. A [Veritas enterprise Vault.Cloud SSO támogatási csapatának](https://www.veritas.com/support/.html) használata a felhasználók hozzáadásához a veritas Enterprise Vault.Cloud SSO platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez a rendszer átirányítja a Veritas Enterprise Vault.cloud SSO bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen a Veritas Enterprise Vault.cloud SSO bejelentkezési URL-címére közvetlenül, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Veritas Enterprise Vault.cloud SSO csempére kattint, a rendszer átirányítja a Veritas Enterprise Vault.cloud SSO bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

A Veritas Enterprise Vault.cloud SSO konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
