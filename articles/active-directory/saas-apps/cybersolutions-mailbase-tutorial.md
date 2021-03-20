---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a CyberSolutions MAILBASEΣ \ CMSS | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és CyberSolutions MAILBASEΣ \ CMSS között.
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
ms.openlocfilehash: 9a1bed217f12646687654f37145a4a796d0487a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455000"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cybersolutions-mailbasecmss"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a CyberSolutions MAILBASEΣ \ CMSS

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a CyberSolutions MAILBASEΣ \ CMSS-et Azure Active Directory (Azure AD) használatával. Ha integrálja a CyberSolutions MAILBASEΣ \ CMSS-t az Azure AD-vel, a következőket teheti:

* A CyberSolutions MAILBASEΣ \ CMSS eléréséhez hozzáférő Azure AD-beli vezérlés.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezzenek a CyberSolutions MAILBASEΣ \ CMSS-be az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* CyberSolutions MAILBASEΣ \ CMSS egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A CyberSolutions MAILBASEΣ \ CMSS támogatja **az SP-t és a identitásszolgáltató-** t KEZDEMÉNYEZte SSO

## <a name="adding-cybersolutions-mailbasecmss-from-the-gallery"></a>CyberSolutions MAILBASEΣ \ CMSS hozzáadása a gyűjteményből

A CyberSolutions MAILBASEΣ \ CMSS integrációjának az Azure AD-be való konfigurálásához hozzá kell adnia a CyberSolutions MAILBASEΣ \ CMSS a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **CYBERSOLUTIONS MAILBASEΣ \ CMSS** kifejezést a keresőmezőbe.
1. Válassza az **CYBERSOLUTIONS MAILBASEΣ \ CMSS** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-cybersolutions-mailbasecmss"></a>Azure AD SSO konfigurálása és tesztelése a CyberSolutions MAILBASEΣ \ CMSS

Konfigurálja és tesztelje az Azure AD SSO-t a CyberSolutions MAILBASEΣ \ CMSS-mel a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a CyberSolutions MAILBASEΣ \ CMSS-ben.

Az Azure AD SSO konfigurálásához és teszteléséhez a CyberSolutions MAILBASEΣ \ CMSS használatával végezze el a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[CyberSolutions-MAILBASE SSO konfigurálása](#configure-cybersolutions-mailbase-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[CyberSolutions-MAILBASE-tesztelési felhasználó létrehozása](#create-cybersolutions-mailbase-test-user)** – ha a felhasználó Azure ad-képviseletéhez csatolt B. Simon-ügyféllel rendelkezik, CyberSolutions MAILBASEΣ \ CMSS.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **CYBERSOLUTIONS MAILBASEΣ \ CMSS** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.cybercloud.jp/saml/module.php/saml/sp/metadata.php/mb_generic_sp`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.cybercloud.jp/cgi-bin/mbase/mblogin/saml2-acs/mb_generic_sp`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<SUBDOMAIN>.cybercloud.jp/cgi-bin/mbase/mblogin?saml_domain=<domain>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a [CYBERSOLUTIONS MAILBASEΣ \ CMSS ügyfél-támogatási csapatához](mailto:tech@cybersolutions.co.jp) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **CYBERSOLUTIONS MAILBASEΣ \ CMSS beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a CyberSolutions MAILBASEΣ \ CMSS.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **CYBERSOLUTIONS MAILBASEΣ \ CMSS** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-cybersolutions-mailbase-sso"></a>CyberSolutions MAILBASE SSO konfigurálása

Ha az egyszeri bejelentkezést a **CYBERSOLUTIONS MAILBASEΣ \ CMSS** oldalon szeretné konfigurálni, el kell küldenie a letöltött **összevonási metaadatokat tartalmazó XML-fájlt** és a megfelelő másolt url-címeket a Azure Portalból a [CyberSolutions MAILBASEΣ \ CMSS support csapatba](mailto:tech@cybersolutions.co.jp). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-cybersolutions-mailbase-test-user"></a>CyberSolutions MAILBASE-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a CyberSolutions MAILBASEΣ \ CMSS. A [CYBERSOLUTIONS MAILBASEΣ \ CMSS támogatási csapatával](mailto:tech@cybersolutions.co.jp) felveheti a felhasználókat a CyberSolutions MAILBASEΣ \ CMSS platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a CyberSolutions MAILBASEΣ \ CMSS bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a CyberSolutions MAILBASEΣ \ CMSS bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a CyberSolutions MAILBASEΣ \ CMSS, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft Access panel használatával bármilyen módban tesztelheti az alkalmazást. Ha a hozzáférési panelen a CyberSolutions MAILBASEΣ \ CMSS csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a CyberSolutions MAILBASEΣ \ CMSS, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

Ha a CyberSolutions MAILBASEΣ \ CMSS konfigurálta, akkor kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).