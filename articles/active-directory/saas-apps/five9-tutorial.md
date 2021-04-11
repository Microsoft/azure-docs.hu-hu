---
title: 'Oktatóanyag: Azure Active Directory integráció a Five9 Plus adapterrel (CTI, Contact Center Agents) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Five9 plusz adapter (CTI, Contact Center-ügynökök) között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 85e953951d5368dc97312e7810f3c356bda7c6b6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218719"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Oktatóanyag: Azure Active Directory integráció a Five9 Plus adapterrel (CTI, Contact Center Agents)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Five9 Plus adaptert (CTI, Contact Center Agents) a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Five9 Plus adaptert (CTI, Contact Center Agents) az Azure AD-vel, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Five9 Plus adapterhez (CTI, Contact Center Agents).
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Five9 Plus adapterbe (CTI, Contact Center Agents) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Five9 Plus-adapterrel (CTI, Contact Center-ügynökökkel) való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Five9 Plus adapter (CTI, Contact Center-ügynökök) egyszeri bejelentkezésre alkalmas előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Five9 Plus adapter (CTI, Contact Center Agents) támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Five9 Plus-adapter (CTI, Contact Center Agents) hozzáadása a katalógusból

A Five9 Plus-adapter (CTI, Contact Center-ügynökök) Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Five9 Plus adaptert (CTI, Contact Center Agents) a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe a **Five9 Plus adapter (CTI, Contact Center-ügynökök)** kifejezést.
1. Válassza ki az **Five9 Plus adaptert (CTI, Contact Center Agents)** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-five9-plus-adapter-cti-contact-center-agents"></a>Azure AD SSO konfigurálása és tesztelése Five9 Plus adapterhez (CTI, Contact Center Agents)

Konfigurálja és tesztelje az Azure AD SSO-t a Five9 Plus adapterrel (CTI, Contact Center Agents) egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Five9 Plus adapterben (CTI, Contact Center Agents).

Az Azure AD SSO és a Five9 Plus adapter (CTI, Contact Center Agents) konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[Five9 Plus adapter (CTI, Contact Center Agents) konfigurálása egyszeri](#configure-five9-plus-adapter-cti-contact-center-agents-sso)** bejelentkezéshez – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Five9 Plus adaptert (CTI, Contact Center Agents) tesztelési felhasználó](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** – ha a felhasználó Azure ad-képviseletéhez csatolt b. Simon-Five9 plusz adapter (CTI, Contact Center Agents) partnere.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Five9 Plus adapter (CTI, Contact Center-ügynökök)** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszeri Sign-On beállítása az SAML-vel** lapon hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az alábbi URL-címek egyikét:
    
    |    Környezet      |       URL-cím      |
    | :-- | :-- |
    | "Five9 Plus adapter a Microsoft Dynamics CRM-hez" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | A "Five9 Plus adapter for zendesk" kifejezésre | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | "Az ügynök asztali eszközkészletének" Five9 Plus adapteréhez | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. A **Válasz URL-címe** szövegmezőbe írja be az alábbi URL-címek egyikét:

    |      Környezet     |      URL-cím      |
    | :--                  | :--           |
    | "Five9 Plus adapter a Microsoft Dynamics CRM-hez" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | A "Five9 Plus adapter for zendesk" kifejezésre | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | "Az ügynök asztali eszközkészletének" Five9 Plus adapteréhez | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **Five9 és-adapter beállítása (CTI, Contact Center ügynökök)** szakaszban másolja a megfelelő URL-címeket (ka) t a követelménynek megfelelően.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a Five9 Plus adapter (CTI, Contact Center Agents) elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **Five9 plusz adapter (CTI, Contact Center ügynökök)** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-five9-plus-adapter-cti-contact-center-agents-sso"></a>Az Five9 Plus adapter (CTI, Contact Center Agents) konfigurálása egyszeri bejelentkezéshez

1. Ha be szeretné állítani az egyszeri bejelentkezést a **Five9 Plus-adapteren (CTI, Contact Center-ügynökök)** , el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-t (ka) t a [Five9 Plus adapter (CTI, Contact Center Agents) támogatási csapatának](https://www.five9.com/about/contact). Emellett az SSO további konfigurálásához kövesse az alábbi lépéseket az adapternek megfelelően:

    a. "Five9 Plus adapter az ügynök asztali eszközkészletéhez" rendszergazdai útmutató: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 Plus adapter a Microsoft Dynamics CRM-hez" rendszergazdai útmutató: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 Plus adapter a zendesk" rendszergazdai útmutató: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Five9 plusz adapter létrehozása (CTI, Contact Center Agents) test User

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Five9 Plus adapterben (CTI, Contact Center Agents). A [Five9 Plus adapter (CTI, Contact Center Agents) támogatási csapatának](https://www.five9.com/about/contact) használata a felhasználók hozzáadásához a Five9 Plus adapter (CTI, Contact Center Agents) platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie a Five9 Plus adapterbe (CTI, Contact Center Agents), amelyhez be kell állítania az egyszeri bejelentkezést.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Five9 plusz adapterére (CTI, Contact Center Agents) csempére kattint, automatikusan be kell jelentkeznie a Five9 Plus-adapterbe (CTI, Contact Center Agents), amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

Ha a Five9 Plus adaptert (CTI, Contact Center Agents) konfigurálta, akkor kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
