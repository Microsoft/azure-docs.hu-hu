---
title: 'Oktatóanyag: Azure Active Directory integráció a HubSpot-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és HubSpot között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 91dfdcef01a121c8282b8fad2e75f67989b75dc3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98733250"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Oktatóanyag: Azure Active Directory integráció a HubSpot

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a HubSpot a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az HubSpot-t az Azure AD-vel, a következőket teheti:

* A HubSpot-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a HubSpot az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció HubSpot való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) a Kezdés előtt.
* Egyszeri bejelentkezést használó HubSpot-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben konfigurálja és teszteli, és integrálja az HubSpot-t az Azure AD-vel.

A HubSpot a következő funkciókat támogatja:

* **Az SP által kezdeményezett egyszeri bejelentkezés**
* **IDENTITÁSSZOLGÁLTATÓ – kezdeményezett egyszeri bejelentkezés**

## <a name="adding-hubspot-from-the-gallery"></a>HubSpot hozzáadása a gyűjteményből

A HubSpot Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a HubSpot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **HubSpot** kifejezést a keresőmezőbe.
1. Válassza ki a **HubSpot** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-hubspot"></a>Azure AD SSO konfigurálása és tesztelése a HubSpot-hez

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az HubSpot-mel konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a HubSpot-ben.

Az Azure AD egyszeri bejelentkezés HubSpot való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

| Feladat | Leírás |
| --- | --- |
| **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)** | Lehetővé teszi a felhasználók számára a funkció használatát. |
| **[HubSpot egyszeri bejelentkezés konfigurálása](#configure-hubspot-single-sign-on)** | Az egyszeri bejelentkezési beállításokat konfigurálja az alkalmazásban. |
| **[Azure AD-tesztkörnyezet létrehozása](#create-an-azure-ad-test-user)** | Teszteli az Azure AD egyszeri bejelentkezést egy Britta Simon nevű felhasználó számára. |
| **[Az Azure AD-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** | Lehetővé teszi a Britta Simon számára az Azure AD egyszeri bejelentkezés használatát. |
| **[HubSpot-teszt felhasználó létrehozása](#create-a-hubspot-test-user)** | Létrehoz egy, a felhasználó Azure AD-képviseletéhez kapcsolódó Britta Simon-t a HubSpot-ben. |
| **[Az egyszeri bejelentkezés tesztelése](#test-single-sign-on)** | Ellenőrzi, hogy a konfiguráció működik-e. |

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

1. A Azure Portal **HubSpot** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** panelen, a *identitásszolgáltató-kezdeményezésű mód* konfigurálásához hajtsa végre a következő lépéseket:

    1. Az **azonosító** mezőbe írjon be egy URL-címet, amely a következő mintával rendelkezik: https: \/ /API.HubSpot.com/login-API/v1/SAML/login?portalId = \<CUSTOMER ID\> .

    1. A **Válasz URL-címe** mezőbe írjon be egy URL-címet, amely a következő mintával rendelkezik: https: \/ /API.HubSpot.com/login-API/v1/SAML/ACS?portalId = \<CUSTOMER ID\> .

    ![HubSpot tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    > [!NOTE]
    > Az URL-címek formázásához tekintse meg a Azure Portal **alapszintű SAML-konfiguráció** paneljén látható mintákat is.

1. Az alkalmazás konfigurálása *SP-kezdeményezésű* módban:

    1. Válassza a **további URL-címek beállítása** lehetőséget.

    1. A **bejelentkezési URL-cím** mezőbe írja be a **https: \/ /app.HubSpot.com/login** értéket.

    ![A további URL-címek beállítása lehetőség](common/metadata-upload-additional-signon.png)

1. Az **egyszeri Sign-On beállítása az SAML** -panellel panelen az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** a **tanúsítvány mellett (Base64)** elemet. A követelmények alapján válasszon egy letöltési lehetőséget. Mentse a tanúsítványt a számítógépére.

    ![A tanúsítvány (Base64) letöltési lehetősége](common/certificatebase64.png)

1. A **HubSpot beállítása** szakaszban másolja a következő URL-címeket a követelmények alapján:

    * Bejelentkezési URL
    * Azure AD-azonosító
    * Kijelentkezési URL-cím

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>HubSpot egyszeri bejelentkezés konfigurálása

1. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a HubSpot rendszergazdai fiókjába.

1. Válassza a **Beállítások** ikont az oldal jobb felső sarkában.

    ![A beállítások ikon a HubSpot](./media/hubspot-tutorial/config1.png)

1. Válassza a **fiók Alapértelmezések** lehetőséget.

    ![A fiók alapértékei beállítás a HubSpot](./media/hubspot-tutorial/config2.png)

1. Görgessen le a **Biztonság** szakaszhoz, majd válassza a **beállítás** lehetőséget.

    ![A beállítás beállítása a HubSpot](./media/hubspot-tutorial/config3.png)

1. Az **egyszeri bejelentkezés beállítása** szakaszban hajtsa végre a következő lépéseket:

    1. A **célközönség URl-címe (szolgáltatói entitás azonosítója)** mezőben válassza a **Másolás** lehetőséget az érték másolásához. A Azure Portal az **alapszintű SAML-konfiguráció** panelen illessze be az értéket az **azonosító** mezőbe.

    1. A **bejelentkezési URL-cím, ACS, címzett vagy átirányítás** mezőben válassza a **Másolás** lehetőséget az érték másolásához. A Azure Portal az **alapszintű SAML-konfiguráció** panelen illessze be az értéket a **Válasz URL-címe** mezőbe.

    1. A HubSpot-ben az azonosító **szolgáltató azonosítója vagy a kiállító URL-címe** mezőben illessze be a Azure Portalba másolt **Azure ad-azonosító** értékét.

    1. A HubSpot-ben az **identitás-szolgáltató egyetlen Sign-On URL-címe** mezőben illessze be a Azure Portalba másolt **bejelentkezési URL-cím** értékét.

    1. A Windows Jegyzettömb alkalmazásban nyissa meg a letöltött tanúsítvány (Base64) fájlt. Válassza ki és másolja ki a fájl tartalmát. Ezután a HubSpot illessze be az **X. 509-tanúsítvány** mezőbe.

    1. Válassza az **Ellenőrzés** lehetőséget.

        ![Az egyszeri bejelentkezés beállítása szakasz a HubSpot-ben](./media/hubspot-tutorial/config4.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a HubSpot.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **HubSpot** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-a-hubspot-test-user"></a>HubSpot-teszt felhasználó létrehozása

Ha engedélyezni szeretné, hogy az Azure AD-felhasználó bejelentkezzen a HubSpot, a felhasználónak a HubSpot-ben kell kiépíteni. A HubSpot-ben a kiépítés manuális feladat.

Felhasználói fiók kiépítése a HubSpot-ben:

1. Jelentkezzen be a HubSpot vállalati webhelyre rendszergazdaként.

1. Válassza a **Beállítások** ikont az oldal jobb felső sarkában.

    ![A beállítások ikon a HubSpot](./media/hubspot-tutorial/config1.png)

1. Válassza a **felhasználók & csapatok** lehetőséget.

    ![A felhasználók & csapatok lehetőség a HubSpot](./media/hubspot-tutorial/user1.png)

1. Válassza a **felhasználó létrehozása** lehetőséget.

    ![A felhasználó létrehozása lehetőség a HubSpot](./media/hubspot-tutorial/user2.png)

1. Az **e-mail addess (ek) hozzáadása** mezőben adja meg a felhasználó e-mail-címét a következő formátumban: brittasimon \@ contoso.com, majd kattintson a **tovább** gombra.

    ![Az e-mail cím (ek) hozzáadása a HubSpot felhasználók létrehozása szakaszában](./media/hubspot-tutorial/user3.png)

1. A **felhasználók létrehozása** szakaszban válassza ki az egyes lapokat. Az egyes lapokon állítsa be a megfelelő beállításokat és engedélyeket a felhasználó számára. Ezután válassza a **tovább** lehetőséget.

    ![A HubSpot felhasználók létrehozása szakaszának lapjai](./media/hubspot-tutorial/user4.png)

1. A meghívás a felhasználónak való elküldéséhez válassza a **Küldés** lehetőséget.

    ![A Küldés lehetőség a HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > A felhasználó akkor aktiválódik, ha a felhasználó elfogadja a meghívást.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a HubSpot bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a HubSpot bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a HubSpot, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások HubSpot csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat kezdeményezéséhez, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra a HubSpot, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A HubSpot konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-aad).