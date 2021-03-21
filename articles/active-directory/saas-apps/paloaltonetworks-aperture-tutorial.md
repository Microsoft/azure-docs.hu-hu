---
title: 'Oktatóanyag: Azure Active Directory az integrációt a Palo Alto Networks-Blendtel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Palo Alto Networks – blende között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 7b4c8ab55a9b2afc9c1a215236fa1276a630a5cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963621"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Oktatóanyag: Azure Active Directory a Palo Alto Networks – blende integrációval

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Palo Alto Networks-blendét Azure Active Directory (Azure AD) használatával.
A Palo Alto Networks – blende Azure AD-vel való integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja a Palo Alto Networks-blende elérését.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Palo Alto Networks-blende (egyszeri bejelentkezés) szolgáltatásba az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció és a Palo Alto Networks – blende konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Palo Alto Networks – blende egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Palo Alto hálózatok – a blende támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Palo Alto-hálózatok hozzáadása – blende a katalógusból

A Palo Alto Networks-blende Azure AD-be való integrálásának konfigurálásához a katalógusból a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a Palo Alto Networks-blende elemet.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **Palo Alto Networks-blende** a keresőmezőbe.
1. Válassza a **Palo Alto Networks-Aperture** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD SSO konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a Palo Alto Networks-Aperture segítségével konfigurálja és teszteli a **B. Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a hozzá tartozó, a Palo Alto hálózatokban található kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a Palo Alto Networks-Aperture használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    * **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. A **[Palo Alto Networks-blende SSO konfigurálása](#configure-palo-alto-networks---aperture-sso)** – az alkalmazás oldalának egyetlen Sign-On beállításainak konfigurálása.
    * A **[Palo Alto Networks-blende test User](#create-palo-alto-networks---aperture-test-user)** -, hogy a Britta Simon a Palo Alto Networks-Aperture, amely a felhasználó Azure ad-képviseletéhez kapcsolódik.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Palo Alto Networks-blende** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely az "alapszintű S L konfiguráció" kifejezést jeleníti meg az "azonosító" és "válasz U R L" szövegmezők kiemelésével, valamint a "Mentés" művelet kiválasztásával.](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Palo Alto Networks – Aperture tartomány és URL-címek egyszeri bejelentkezési adatai SP](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. A következő értékek beszerzéséhez vegye fel a kapcsolatot a [Palo Alto Networks-blende ügyfélszolgálati csapatával](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **Palo Alto Networks-Aperture beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a Palo Alto Networks-Aperture elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Palo Alto Networks-blende** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-palo-alto-networks---aperture-sso"></a>A Palo Alto Networks – blende SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Palo Alto Networks-blende szolgáltatásba rendszergazdaként.

2. A felső menüsorban kattintson a **Beállítások** elemre.

    ![A beállítások lap](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. Navigáljon az **alkalmazás** szakaszra a menü bal oldalán kattintson a **hitelesítési** űrlap elemre.

    ![Az Auth lap](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. A **hitelesítés** oldalon hajtsa végre a következő lépéseket:
    
    ![A hitelesítés lap](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Jelölje be az egyszeri bejelentkezés **engedélyezése (támogatott SSP-szolgáltatók okta, egy bejelentkezés)** lehetőséget az **egyszeri bejelentkezés** mezőből.

    b. Az **identitás-szolgáltató azonosítója** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portalból másolt.

    c. Kattintson a **fájl kiválasztása** lehetőségre a letöltött tanúsítvány Azure ad-ből való feltöltéséhez az **Identity Provider tanúsítvány** mezőjében.

    d. Az **Identity Provider SSO URL** szövegmezőben illessze be a **bejelentkezési URL**-címet, amelyet a Azure Portalból másolt.

    e. Tekintse át a identitásszolgáltató információit a **blende info** szakaszban, majd töltse le a tanúsítványt a **apertúra kulcs** mezőjéből.

    f. Kattintson a **Mentés** gombra.


### <a name="create-palo-alto-networks---aperture-test-user"></a>Palo Alto-hálózatok létrehozása – apertúra tesztelése felhasználó

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Palo Alto Networks-blende-ben. A [Palo Alto Networks-blende ügyfél-támogatási csapatának](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) használata a felhasználók hozzáadásához a Palo Alto Networks-blende platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a Palo Alto Networks-blende bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Nyissa meg közvetlenül a Palo Alto Networks-blende bejelentkezési URL-címét, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie a Palo Alto Networks-Aperture-be, amelyhez be kell állítania az egyszeri bejelentkezést 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazásokban a Palo Alto Networks-blende csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a Palo Alto Networks-Aperture-be, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A Palo Alto Networks – blende beállítása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).