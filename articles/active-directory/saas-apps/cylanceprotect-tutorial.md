---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a CylancePROTECT | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory CylancePROTECT és a CylancePROTECT között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: a5daf83346db901288d5bc614f341c354d0c008f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481344"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cylanceprotect"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a CylancePROTECT-sel

Ez az oktatóanyag bemutatja, hogyan integrálhatja a CylancePROTECT-t a Azure Active Directory (Azure AD) használatával. Ha integrálja a CylancePROTECT-t az Azure AD-val, a következőt tudja:

* Az Azure AD vezérlése, aki hozzáféréssel rendelkezik a CylancePROTECT-hez.
* Engedélyezze a felhasználók számára, hogy automatikusan bejelentkeznek a CylancePROTECT szolgáltatásba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, egy ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* CylancePROTECT egyszeri bejelentkezés (SSO) által engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO-t konfigurálja és teszteli tesztkörnyezetben.

* A CylancePROTECT támogatja **az internetszolgáltató által kezdeményezett** SSO-t.

> [!NOTE]
> Az alkalmazás azonosítója rögzített sztringérték, így csak egy példány konfigurálható egy bérlőben.

## <a name="add-cylanceprotect-from-the-gallery"></a>A CylancePROTECT hozzáadása a katalógusból

A CylancePROTECT Azure AD-be való integrálásának konfigurálához hozzá kell adni a CylancePROTECT szolgáltatásokat a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a **Vállalati alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a **CylancePROTECT** kifejezéseket a keresőmezőbe.
1. Válassza **a CylancePROTECT lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-cylanceprotect"></a>Az Azure AD SSO konfigurálása és tesztelése a CylancePROTECT-hez

Konfigurálja és tesztelje az Azure AD SSO-t a CylancePROTECT segítségével egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között a CylancePROTECT-ben.

Az Azure AD SSO CylancePROTECT használatával való konfiguráláshoz és teszteléshez hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy lehetővé tegye a felhasználók számára a funkció használatát.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[A CylancePROTECT SSO konfigurálása](#configure-cylanceprotect-sso)** – az egyszeri bejelentkezési beállítások alkalmazásoldalon való konfigurálása.
    1. **[Hozza létre a CylancePROTECT](#create-cylanceprotect-test-user)** tesztfelhasználót, hogy a B.Simon megfelelője legyen a CylancePROTECT-ben, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[Az SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **CylancePROTECT** alkalmazásintegráció lapján keresse meg a **Kezelés** szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon adja meg a következő mezők értékeit:

    a. Az Azonosító **szövegmezőbe** írja be a következő URL-címek egyikét:
    
    | Region | URL-cím értéke |
    |----------|---------|
    | Asia-Pacific (APNE1)| `https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Asia-Pacific (AU) | `https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml` |
    | Közép-Európa (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Észak-Amerika|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Dél-Amerika (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

    b. A Válasz **URL-cím** szövegmezőbe írja be a következő URL-címek egyikét:
    
    | Region | URL-cím értéke |
    |----------|---------|
    | Asia-Pacific (APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Asia-Pacific (AU)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Közép-Európa (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Észak-Amerika|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Dél-Amerika (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

1. A CylancePROTECT alkalmazás az SAML helyességi feltételeket egy adott formátumban várja, amelyhez egyéni attribútumleképezéseket kell hozzáadnia az SAML-jogkivonatok attribútumkonfigurációihoz. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható, ahol az as **nameidentifier** a **user.userprincipalname** névvel van leképezve. A CylancePROTECT alkalmazás elvárja, hogy a **nameidentifier** le legyen leképezve **a user.mail** fájlra, és távolítsa el az összes többi jogcímet, ezért az attribútumleképezés szerkesztéséhez kattintson a Szerkesztés ikonra, és módosítsa az attribútumleképezést. 

    ![image](common/edit-attribute.png)

1. Az Egyszeri bejelentkezés beállítása **SAML-sel** lap **SAML** aláíró tanúsítvány szakaszában keresse meg a  **Tanúsítvány (Base64)** et, majd válassza a Letöltés lehetőséget a tanúsítvány letöltéséhez és a számítógépére mentéséhez.

    ![A Tanúsítvány letöltése hivatkozás](common/certificatebase64.png)

1. A **CylancePROTECT** beállítása szakaszban másolja ki a követelménynek megfelelő URL-címet/URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

> [!NOTE]
> Nyissa meg a letöltött Base64-kódolású tanúsítványt egy szövegszerkesztőben, és csak a **START** és **END** címkék közötti szöveget másolja a Cylance felügyeleti portálba való beillesztéshez.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót fog létrehozni a Azure Portal területen.

1. A bal oldali panelen válassza Azure Portal lehetőséget, **Azure Active Directory** **a Felhasználók** lehetőséget, majd válassza a Minden **felhasználó lehetőséget.**
1. Válassza **az Új felhasználó** lehetőséget a képernyő tetején.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév **mezőbe írja** be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket. 
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezni fogja a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát. Ehhez hozzáférést biztosít a CylancePROTECT-hez.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **CylancePROTECT lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza a** Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-cylanceprotect-sso"></a>A CylancePROTECT SSO konfigurálása

Ha egyszeri bejelentkezést konfigurál a **CylancePROTECT** oldalon, el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-címeket a Azure Portal [CylancePROTECT támogatási csapatának.](https://www.cylance.com/en-us/resources/support/support-overview.html) Ezt a beállítást úgy állítják be, hogy az SAML SSO-kapcsolat megfelelően legyen beállítva mindkét oldalon. További információért használja a Cylance dokumentációját: [https://support.cylance.com/s/](https://support.cylance.com/s/) .

### <a name="create-cylanceprotect-test-user"></a>CylancePROTECT tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a CylancePROTECT-ben. A felhasználók A CylancePROTECT platformon való hozzáadásához működjön együtt a konzol rendszergazdájával. A Azure Active Directory fióktulajdonos kap egy e-mailt, és egy hivatkozással megerősíti a fiókját, mielőtt az aktívvá válik.

## <a name="test-sso"></a>Az SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját.

* Kattintson az Alkalmazás tesztelése elemre a Azure Portal, és automatikusan be kell jelentkezve lennie arra a CylancePROTECT-be, amelyhez beállította az SSO-t.

* Használhatja a Microsoft Saját alkalmazások. Amikor a CylancePROTECT csempére kattint a Saját alkalmazások, automatikusan be kell jelentkezve ahhoz a CylancePROTECT-hez, amelyhez beállította az SSO-t. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.


## <a name="next-steps"></a>Következő lépések

A CylancePROTECT konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
