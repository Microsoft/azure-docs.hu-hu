---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Roadmunk'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Roadmunk között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: a7f4682be2f7fbf308aba32768efa932f27b7a87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181704"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Roadmunk

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Roadmunk a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Roadmunk-t az Azure AD-vel, a következőket teheti:

* A Roadmunk-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók Azure AD-fiókjaik használatával automatikusan bejelentkezzenek a Roadmunk.
* A fiókokat egy központi helyen, a Azure Portal kezelheti.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egyszeri bejelentkezéshez (SSO) engedélyezett Roadmunk-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

A Roadmunk *támogatja a szolgáltató* (SP) és az *Identity Provider* (identitásszolgáltató) által indított egyszeri bejelentkezést.

## <a name="add-roadmunk-from-the-gallery"></a>Roadmunk hozzáadása a gyűjteményből

A Roadmunk az Azure AD-be való integrálásához a katalógusból adja hozzá a Roadmunk a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiók használatával.
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban, a keresőmezőbe írja be a **Roadmunk** kifejezést.
1. Válassza ki a **Roadmunk** az eredmények közül, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>Azure AD SSO konfigurálása és tesztelése a Roadmunk-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Roadmunk egy *B. Simon* nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Roadmunk-ben.

Az alábbiakban áttekintheti, hogyan konfigurálhatja és tesztelheti az Azure AD SSO-t a Roadmunk:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t úgy, hogy a felhasználók használhatják ezt a funkciót.
    1. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad SSO teszteléséhez B. Simon használatával.
    1. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy B. Simon engedélyezze az Azure ad SSO használatát.
1. [Konfigurálja a ROADMUNK SSO](#configure-roadmunk-sso) -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. [Hozzon létre egy Roadmunk-teszt felhasználót](#create-roadmunk-test-user) , hogy csatolja a B. Simon Roadmunk a felhasználó Azure ad-képviseletéhez.
1. A konfiguráció működésének [ellenőrzéséhez ellenőrizze az egyszeri bejelentkezést](#test-sso) .

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO-t a Azure Portalban:

1. A Azure Portal **Roadmunk** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, majd válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az **alapszintű SAML-konfigurációhoz** tartozó toll ikont a beállítások szerkesztéséhez.

   ![Az alapszintű SAML-konfiguráció szerkesztési ikonját ábrázoló képernyőkép.](common/edit-urls.png)

1. Ha az **alapszintű SAML-konfiguráció** szakaszban van egy SP-metaadatok fájlja, és identitásszolgáltató módban kívánja konfigurálni, kövesse az alábbi lépéseket:

    a. Válassza a **metaadat-fájl feltöltése** lehetőséget.

    ![A metaadat-fájl feltöltésére mutató hivatkozást bemutató képernyőkép.](common/upload-metadata.png)

    b. Válassza ki a mappa ikont a "Roadmunk SSO konfigurálása" eljárás 4. lépésében letöltött metaadat-fájl kiválasztásához. Ezután válassza a **Feltöltés** lehetőséget.

    ![A metaadat-fájl kiválasztását bemutató képernyőkép.](common/browse-upload-metadata.png)

    A metaadatok feltöltése után az **alapszintű SAML-konfiguráció** szakaszban az **azonosító** és a **Válasz URL-** értékei automatikusan ki lesznek töltve.

    ![Képernyőfelvétel: az alapszintű SAML-konfiguráció szakasza. Az azonosító mező és a válasz URL-címe mező ki van emelve.](common/idp-intiated.png)

    > [!Note]
    > Ha az **azonosító** és a **Válasz URL-címe** nem lesz automatikusan kitöltve, akkor manuálisan adja meg az értékeket.

1. Ha az alkalmazást SP-kezdeményezésű módban szeretné konfigurálni, válassza a **további URL-címek beállítása** lehetőséget. A **bejelentkezési URL** mezőbe írja be a következőt: `https://login.roadmunk.com`

    ![Képernyőfelvétel: a bejelentkezési URL-cím beállítása az SP által kezdeményezett módhoz.](common/metadata-upload-additional-signon.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját**. Ezután a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![Az SAML aláíró tanúsítvány letöltési hivatkozását ábrázoló képernyőkép.](common/metadataxml.png)

1. A **Roadmunk beállítása** szakaszban másolja ki a szükséges URL-címet vagy URL-címeket.

    ![A konfigurációs URL-címek másolásának helyét bemutató képernyőkép.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal. Nevezze el a *B. Simon* felhasználót.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.
1. Az ablak tetején válassza az **új felhasználó** lehetőséget.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Adja meg például a következőt: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure SSO használatát azáltal, hogy hozzáférést biztosít a Roadmunk.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Roadmunk** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, majd válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **Felhasználó hozzáadása** elemet. Ezután a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanel **felhasználók** listájában válassza ki a **B. Simon** elemet. Ezután a párbeszédpanel alján kattintson a **kiválasztás elemre**.
1. Ha egy szerepkört a felhasználókhoz kíván rendelni, válassza ki azt a **szerepkör kiválasztása** legördülő menüből. Ha nem állított be szerepkört ehhez az alkalmazáshoz, akkor az **alapértelmezett hozzáférési** szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-roadmunk-sso"></a>Roadmunk SSO konfigurálása

1. Jelentkezzen be rendszergazdaként a Roadmunk webhelyre.

1. A lap alján válassza ki a felhasználó ikont, majd válassza a **Fiókbeállítások** lehetőséget.

    ![Képernyőfelvétel: a felhasználói fiókok beállításainak kiválasztása.](./media/roadmunk-tutorial/account.png)

1. Lépjen a **vállalati**  >  **hitelesítési beállítások menüpontra**.

1. A **hitelesítési beállítások** lapon kövesse az alábbi lépéseket:

    ![A hitelesítési beállítások lapot ábrázoló képernyőfelvétel.](./media/roadmunk-tutorial/saml-sso.png)

    a. Az **SAML egyszeri bejelentkezés (SSO)** bekapcsolása.

    b. Az **1. lépés** szakaszban töltse fel a metaadatok XML-fájlját, vagy adja meg a metaadatok URL-címét.

    c. A **2. lépés** szakaszban töltse le a Roadmunk metaadat-fájlt, majd mentse a számítógépre.

    d. Ha az SSO használatával szeretne bejelentkezni, a **3. lépés** szakaszban válassza az SAML- **Sign-In kikényszerítés** lehetőséget.

    e. Kattintson a **Mentés** gombra.


### <a name="create-roadmunk-test-user"></a>Roadmunk-tesztelési felhasználó létrehozása

1. Jelentkezzen be rendszergazdaként a Roadmunk webhelyre.

1. Válassza ki a felhasználó ikont az oldal alján, majd válassza a **Fiókbeállítások** lehetőséget.

    ![A felhasználói fiókok beállításainak megnyitását bemutató képernyőkép.](./media/roadmunk-tutorial/account.png)

1. Nyissa meg a **felhasználók** lapot, majd válassza a **felhasználó meghívása** lehetőséget.

    ![A felhasználók lapot ábrázoló képernyőfelvétel A felhasználó meghívása gomb ki van emelve. A megnyitott ablakban az E-mail és a szerepkör mezők ki vannak emelve.](./media/roadmunk-tutorial/create-user.png)

1. A megjelenő űrlapon adja meg a szükséges információkat, majd válassza a **meghívás** lehetőséget.


## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD SSO konfigurációját a hozzáférési panel használatával teszteli.

A saját alkalmazások portálon, amikor kiválasztja a **Roadmunk** csempét, automatikusan be kell jelentkeznie arra a Roadmunk-fiókba, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ: [Bejelentkezés és alkalmazások indítása a saját alkalmazások portálján](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

A Roadmunk konfigurálása után kényszerítheti a munkamenet-vezérlést. A munkamenet-vezérlő valós időben védi a szervezete bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. 

Megtudhatja, hogyan [kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).