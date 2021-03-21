---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Cisco AnyConnect | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Cisco AnyConnect között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: a89ab7f2304fa51d3e8c7a968d445c9b40a457a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456088"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-anyconnect"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Cisco AnyConnect

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Cisco AnyConnect Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Cisco AnyConnect-t, a következőket teheti:

* A Cisco AnyConnect hozzáférését biztosító Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Cisco AnyConnect az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Cisco AnyConnect egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Cisco AnyConnect támogatja a **identitásszolgáltató** által KEZDEMÉNYEZett SSO-t

## <a name="adding-cisco-anyconnect-from-the-gallery"></a>Cisco-AnyConnect hozzáadása a katalógusból

A Cisco AnyConnect Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Cisco AnyConnect a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Cisco AnyConnect** kifejezést a keresőmezőbe.
1. Válassza ki a **Cisco AnyConnect** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-cisco-anyconnect"></a>Az Azure AD SSO konfigurálása és tesztelése a Cisco AnyConnect

Konfigurálja és tesztelje az Azure AD SSO-t a Cisco AnyConnect egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Cisco AnyConnect.

Az Azure AD SSO és a Cisco AnyConnect konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Cisco ANYCONNECT SSO konfigurálása](#configure-cisco-anyconnect-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Cisco AnyConnect-teszt felhasználó létrehozása](#create-cisco-anyconnect-test-user)** – ha a Cisco AnyConnect található B. Simon partnere, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Cisco AnyConnect** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `< YOUR CISCO ANYCONNECT VPN VALUE >`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `< YOUR CISCO ANYCONNECT VPN VALUE >`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek beszerzéséhez forduljon a [Cisco AnyConnect ügyfél-támogatási csapatához](https://www.cisco.com/c/en/us/support/index.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd válassza a **Letöltés** lehetőséget a tanúsítványfájl letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Cisco AnyConnect beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

> [!NOTE]
> Ha a kiszolgáló több TGT szeretne felvenni, akkor hozzá kell adnia a Cisco AnyConnect alkalmazás több példányát a katalógusból. Emellett a saját tanúsítványát is feltöltheti az Azure AD-ben az összes ilyen alkalmazás-példányra. Így ugyanazzal a tanúsítvánnyal rendelkezhet az alkalmazásokhoz, de minden alkalmazáshoz külön azonosító és válasz URL-címet is beállíthat.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Cisco AnyConnect.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Cisco AnyConnect** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-cisco-anyconnect-sso"></a>Cisco AnyConnect SSO konfigurálása

1. Ezt először a CLI-n hajtja végre, és a ASDM egy másik időpontban is elérhetővé vált.

1. Kapcsolódjon a VPN-berendezéshez, és 9,8-as kódú ASA-t fog használni, a VPN-ügyfelek pedig 4.6 + lesz.

1. Először létre kell hoznia egy Trustpoint, és importálnia kell az SAML-tanúsítványt.

   ```
    config t

    crypto ca trustpoint AzureAD-AC-SAML
      revocation-check none
      no id-usage
      enrollment terminal
      no ca-check
    crypto ca authenticate AzureAD-AC-SAML
    -----BEGIN CERTIFICATE-----
    …
    PEM Certificate Text from download goes here
    …
    -----END CERTIFICATE-----
    quit
   ```

1. A következő parancsok kiépítik az SAML-identitásszolgáltató.

   ```
    webvpn
    saml idp https://sts.windows.net/xxxxxxxxxxxxx/ (This is your Azure AD Identifier from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-in https://login.microsoftonline.com/xxxxxxxxxxxxxxxxxxxxxx/saml2 (This is your Login URL from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-out https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0 (This is Logout URL from the Set up Cisco AnyConnect section in the Azure portal)
    trustpoint idp AzureAD-AC-SAML
    trustpoint sp (Trustpoint for SAML Requests - you can use your existing external cert here)
    no force re-authentication
    no signature
    base-url https://my.asa.com
    ```

1. Most már alkalmazhat SAML-hitelesítést a VPN-alagút konfigurációján.

    ```
    tunnel-group AC-SAML webvpn-attributes
      saml identity-provider https://sts.windows.net/xxxxxxxxxxxxx/
      authentication saml
    end

    write mem
    ```

    > [!NOTE]
    > Az SAML identitásszolgáltató-konfigurációval rendelkezik. Ha módosítja a identitásszolgáltató konfigurációt, el kell távolítania a SAML Identity-Provider konfigurációját az alagút csoportjából, és újra alkalmaznia kell a módosítások érvénybe léptetéséhez.

### <a name="create-cisco-anyconnect-test-user"></a>Cisco AnyConnect-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Cisco AnyConnect-ben. Működjön együtt a [Cisco AnyConnect támogatási csapatával](https://www.cisco.com/c/en/us/support/index.html) , hogy hozzáadja a felhasználókat a Cisco AnyConnect platformhoz. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Cisco AnyConnect, amelyhez be szeretné állítani az egyszeri bejelentkezést
* Használhatja a Microsoft Access panelt. Ha a hozzáférési panelen a Cisco AnyConnect csempére kattint, automatikusan be kell jelentkeznie arra a Cisco AnyConnect, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

A Cisco AnyConnect konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).