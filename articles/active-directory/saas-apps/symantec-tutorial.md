---
title: 'Oktatóanyag: Azure Active Directory Symantec Web Security Service (WSS) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a Symantec Web Security Service (WSS) között.
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
ms.openlocfilehash: af7d126bfdc9ff8edf6b498747fab9c7f497a0f4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484846"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Oktatóanyag: Azure Active Directory a Symantec Web Security Service (WSS) szolgáltatással

Ez az oktatóanyag bemutatja, hogyan integrálhatja Symantec Web Security Service- (WSS-) fiókját az Azure Active Directory- (Azure AD-) fiókkal, hogy a WSS hitelesítse az Azure AD-ban létesített végfelhasználót SAML-hitelesítéssel, és kikényszeríteni tudja a felhasználói vagy csoportszintű szabályzatszabályokat.

A Symantec Web Security Service (WSS) az Azure AD-val való integrálása a következő előnyöket biztosítja:

- A WSS-fiók által használt összes végfelhasználót és csoportot az Azure AD-portálról kezelheti.

- Lehetővé teszi a végfelhasználók számára, hogy hitelesítsék magukat a WSS-ban az Azure AD-beli hitelesítő adataik használatával.

- Engedélyezze a WSS-fiókban definiált felhasználói és csoportszintű házirendszabályok kényszerítét.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, egy ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Symantec Web Security Service (WSS) egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli tesztkörnyezetben.

* A Symantec Web Security Service (WSS) támogatja **az internetszolgáltató által** kezdeményezett SSO-t.

> [!NOTE]
> Az alkalmazás azonosítója rögzített sztringérték, így csak egy példány konfigurálható egy bérlőben.

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Symantec Web Security Service (WSS) hozzáadása a katalógusból

A Symantec Web Security Service (WSS) Azure AD-be való integrálásának konfigurálához hozzá kell adni a Symantec Web Security Service (WSS) szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a **Vállalati alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a **Symantec Web Security Service (WSS)** kifejezéseket a keresőmezőbe.
1. Válassza a **Symantec Web Security Service (WSS)** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-symantec-web-security-service-wss"></a>Az Azure AD SSO konfigurálása és tesztelése a Symantec Web Security Service (WSS) szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t a Symantec Web Security Service (WSS) szolgáltatással egy **B.Simon** nevű tesztfelhasználóval. Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a Symantec Web Security Service (WSS) kapcsolódó felhasználója között.

Ha az Azure AD SSO-t a Symantec Web Security Service (WSS) szolgáltatással konfigurálja és teszteli, hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy lehetővé tegye a felhasználók számára a funkció használatát.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[A Symantec Web Security Service (WSS) SSO](#configure-symantec-web-security-service-wss-sso)** konfigurálása – az egyszeri bejelentkezési beállítások alkalmazásoldalon való konfigurálásához.
    1. **[Symantec Web Security Service (WSS)](#create-symantec-web-security-service-wss-test-user)** tesztfelhasználó létrehozása – a B.Simon megfelelője a Symantec Web Security Service (WSS) szolgáltatásban, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[Az SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **a Symantec Web Security Service (WSS)** alkalmazásintegrációs oldalán keresse meg a Kezelés szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.** 
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

4. Az **SAML-alapkonfiguráció** párbeszédpanelen hajtsa végre a következő lépéseket:

    a. Az Azonosító **szövegmezőbe** írja be az URL-címet: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. A Válasz **URL-cím** szövegmezőbe írja be az URL-címet: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Lépjen kapcsolatba a [Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) ügyféltámogatási csapatával. Az **Azonosító** és a Válasz **URL-cím** értékei valamilyen okból nem működnek. A minta az **SAML** alapkonfiguráció szakaszában látható mintákat is Azure Portal.

5. Az Egyszeres hitelesítés beállítása **samlSign-On val** lapon, az **SAML** aláíró tanúsítvány szakaszban kattintson a Letöltés gombra az összevonási metaadatok **XML-fájljának** letöltéséhez a megadott beállításokból igény szerint, majd mentse a számítógépre. 

    ![A Tanúsítvány letöltése hivatkozás](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót fog létrehozni a Azure Portal területen.

1. A bal oldali panelen válassza a Azure Portal **lehetőséget,** Azure Active Directory **a Felhasználók** lehetőséget, majd válassza a Minden **felhasználó lehetőséget.**
1. Válassza **a képernyő tetején** található Új felhasználó lehetőséget.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév **mezőbe írja** be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket. 
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát. Ehhez hozzáférést biztosít a Symantec Web Security Service (WSS) szolgáltatáshoz.

1. A Azure Portal válassza a Vállalati **alkalmazások,** majd a Minden **alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **Symantec Web Security Service (WSS) lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza** a Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-symantec-web-security-service-wss-sso"></a>Symantec Web Security Service (WSS) SSO konfigurálása

Ha egyszeri bejelentkezést konfigurál a Symantec Web Security Service (WSS) oldalán, tekintse meg a WSS online dokumentációját. A letöltött **összevonási metaadatok** XML-fájlját importálni kell a WSS-portálra. Ha segítségre van szüksége a WSS-portálon található konfigurációval kapcsolatban, lépjen kapcsolatba a [Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) támogatási csapatával.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Symantec Web Security Service- (WSS-) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Symantec Web Security Service (WSS) szolgáltatásban. A megfelelő végfelhasználói felhasználónév manuálisan is létre lehet hozva a WSS-portálon, vagy megvárhatja, amíg az Azure AD-ben létesített felhasználók/csoportok néhány perc (~15 perc) után szinkronizálódnak a WSS-portálra. Az egyszeri bejelentkezés használata előtt a felhasználókat létre kell hoznunk és aktiválnunk kell. A végfelhasználói gép nyilvános IP-címét, amelyet webhelyek tallózására fog használni, a Symantec Web Security Service (WSS) portálon is létre kell hozni.

> [!NOTE]
> Kattintson ide [a](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) gép nyilvános IPad-címének lekért címért.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját.

* Kattintson az Alkalmazás tesztelése a Azure Portal lehetőségre, és automatikusan be kell jelentkezve lennie a Symantec Web Security Service (WSS) szolgáltatásba, amelyhez beállította az SSO-t.

* Használhatja a Microsoft Saját alkalmazások. Amikor a Saját alkalmazások-ban a Symantec Web Security Service (WSS) csempére kattint, automatikusan be kell jelentkezve lennie a Symantec Web Security Service (WSS) szolgáltatásba, amelyhez beállította az SSO-t. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

A Symantec Web Security Service (WSS) konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
