---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása az Check Point Identity Awareness | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és az Check Point Identity Awareness között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 68705d4d1f870820e30563d2a197ac56349d2445
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520919"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-identity-awareness"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása az Check Point Identity Awarenessvel

Ez az oktatóanyag bemutatja, hogyan integrálhatja a Check Point Identity Awarenesset a Azure Active Directory (Azure AD) használatával. Az Identity Awareness Check Point az Azure AD-val való integrálásakor a következőt tudja:

* Az Azure AD-beli irányítás, aki hozzáféréssel rendelkezik Check Point Identitástudatossághoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve Check Point Identity Awareness szolgáltatásba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Check Point Identity Awareness egyszeri bejelentkezést (SSO) engedélyező előfizetést.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* Check Point Identity Awareness támogatja az **SP által** kezdeményezett SSO-t.

## <a name="adding-check-point-identity-awareness-from-the-gallery"></a>Identitás Check Point hozzáadása a katalógusból

Az Check Point Identity Awareness Azure AD-be való integrálásának konfigurálához fel kell vennie az Check Point Identity Awarenesset a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a **Check Point a** keresőmezőbe.
1. Válassza **Check Point Identity Awareness (Identitástudatosság) lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.


## <a name="configure-and-test-azure-ad-sso-for-check-point-identity-awareness"></a>Az Azure AD SSO konfigurálása és tesztelése Check Point identitástudatossághoz

Konfigurálja és tesztelje az Azure AD SSO-t Check Point Identity Awareness használatával egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Check Point identitástudatosságban.

Ha az Azure AD SSO-t az Check Point identitástudatosság használatával konfigurálja és teszteli, hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használják ezt a funkciót.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[Konfigurálja Check Point identity awareness SSO-t](#configure-check-point-identity-awareness-sso)** – az egyszeri bejelentkezési beállítások alkalmazásoldali konfigurálásához.
    1. **[Hozzon Check Point Identity Awareness](#create-check-point-identity-awareness-test-user)** tesztfelhasználót – hogy a B.Simon megfelelője legyen az Check Point Identity Awarenessben, amely a felhasználó Azure AD-ábrázolásához kapcsolódik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal a **Check Point Identity Awareness** alkalmazásintegrációs oldalán keresse meg a **Kezelés** szakaszt, és válassza az egyszeri **bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. A Set **up single sign-on with SAML** (Egyszeri bejelentkezés beállítása SAML-sel) oldalon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

1. Az **SAML-alapkonfiguráció szakaszban** adja meg a következő mezők értékeit:

    a. Az Azonosító **(Entitásazonosító) szövegmezőbe** írjon be egy URL-címet a következő mintával: `https://<GATEWAY_IP>/connect/spPortal/ACS/ID/<IDENTIFIER_UID>`

    b. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://<GATEWAY_IP>/connect/spPortal/ACS/Login/<IDENTIFIER_UID>`

    c. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://<GATEWAY_IP>/connect`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címével. Ezeket [az Check Point az Identity Awareness Client támogatási csapatának](mailto:support@checkpoint.com) elérhetőségét. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

1. Az Egyszeri bejelentkezés beállítása **SAML-sel** lap **SAML** aláíró tanúsítvány szakaszában keresse meg a  **Tanúsítvány (Base64)** et, majd válassza a Letöltés lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A Tanúsítvány letöltése hivatkozás](common/certificatebase64.png)

1. A **Beállítás Check Point Identity Awareness szakaszban** másolja ki a követelménynek megfelelő URL-címet/URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)
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

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát, ha hozzáférést biztosít Check Point Identity Awareness szolgáltatáshoz.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza az **Identitástudatosság Check Point lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza** a Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja azt **a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör lesz kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-check-point-identity-awareness-sso"></a>Az Check Point SSO konfigurálása

1. Jelentkezzen be a Check Point Identity Awareness vállalati webhelyre rendszergazdaként.

1. A SmartConsole > **Gateways & Servers** (Átjárók és kiszolgálók) nézetben kattintson a New > More > **User/Identity > Identity Provider**(Új >)) elemre.

    ![képernyőkép az új identitásszolgáltatóról.](./media/check-point-identity-awareness-tutorial/identity-provider.png)

1. Hajtsa végre a következő lépéseket az **Új identitásszolgáltató ablakban.**

    ![képernyőkép az Identitásszolgáltató szakaszról.](./media/check-point-identity-awareness-tutorial/new-identity-provider.png)

    a. Az Átjáró **mezőben** válassza ki azt a biztonsági átjárót, amelynek SAML-hitelesítést kell végrehajtania.

    b. A **Szolgáltatás mezőben** válassza az **Identity Awareness (Identitástudatosság)** lehetőséget a legördülő menüből.

    c. Másolja **az Identifier(Entity ID) (Azonosító(Entitásazonosító)** értékét, illessze be ezt az értéket a fájl Alapszintű **SAML-konfiguráció** szakaszában található Azonosító Azure Portal. 

    d. Másolja **ki a Válasz URL-cím** értékét, és illessze be ezt az értéket a **válasz-URL-cím** szövegmezőbe az alapszintű **SAML-konfiguráció** szakaszban a Azure Portal.

    e. Válassza **a Metaadatfájl importálása lehetőséget** a letöltött tanúsítvány **(Base64)** feltöltéséhez a Azure Portal.

    > [!NOTE]
    > A Manuális beszúrás lehetőség kiválasztásával  manuálisan is beillesztheti az Entitásazonosító és  a Bejelentkezési **URL-cím** értékeket a megfelelő mezőkbe, és feltöltheti a tanúsítványfájlt a Azure Portal. 

    f. Kattintson az **OK** gombra.

### <a name="create-check-point-identity-awareness-test-user"></a>Identitástudatossági tesztfelhasználó Check Point létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Check Point Identity Awareness (Identitástudatosság) területen. Működjön együtt [Check Point Identity Awareness](mailto:support@checkpoint.com) támogatási csapatával, hogy hozzáadja a felhasználókat az Check Point Identity Awareness platformhoz. Az egyszeri bejelentkezés használata előtt a felhasználókat létre kell hoznunk és aktiválnunk kell.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja Check Point Identity Awareness Bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül Check Point az Identity Awareness Sign-on URL-címre, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft Saját alkalmazások. Amikor a Check Point Identity Awareness (Identitástudatosság) csempére Saját alkalmazások, a rendszer átirányítja a Check Point Identity Awareness Sign-on URL-címre. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.


## <a name="next-steps"></a>Következő lépések

Miután konfigurálta Check Point Identity Awarenesset, kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)


