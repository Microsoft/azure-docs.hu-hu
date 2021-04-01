---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Velpic SAML-vel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Velpic SAML között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.openlocfilehash: 81523a1c11b6a3da4e7d1db5d8249921ad5047ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92635864"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Velpic SAML használatával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Velpic SAML-t Azure Active Directory (Azure AD-val). Ha integrálja a Velpic SAML-t az Azure AD-vel, a következőket teheti:

* Azon Azure AD-beli vezérlés, akik hozzáférhetnek az SAML-Velpic.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az SAML-Velpic az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Velpic SAML egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Velpic SAML támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-velpic-saml-from-the-gallery"></a>A Velpic SAML hozzáadása a gyűjteményből

A Velpic SAML Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Velpic SAML-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Velpic SAML** kifejezést a keresőmezőbe.
1. Válassza ki a **VELPIC SAML** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az SAML-Velpic

Konfigurálja és tesztelje az Azure AD SSO-t a Velpic SAML használatával egy **B. Simon** nevű teszt felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a Velpic SAML-ban.

Az Azure AD SSO Velpic SAML-vel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[VELPIC SAML SSO konfigurálása](#configure-velpic-saml-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy VELPIC SAML-teszt felhasználót](#create-velpic-saml-test-user)** – hogy rendelkezzen a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Velpic SAML-ügyféllel.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Velpic SAML** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<sub-domain>.velpicsaml.net`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Vegye figyelembe, hogy a bejelentkezési URL-címet a Velpic SAML-csapat adja meg, és az azonosító értéke akkor lesz elérhető, ha az egyszeri bejelentkezés beépülő modult a Velpic SAML-oldalon konfigurálja. Ezt az értéket a Velpic SAML-alkalmazás oldaláról kell másolnia, és be kell illesztenie ide.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **VELPIC SAML beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít az SAML-Velpic.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **VELPIC SAML** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-velpic-saml-sso"></a>Velpic SAML SSO konfigurálása

1. A Velpic SAML-n belüli konfigurációjának automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **VELPIC SAML beállítása** elemre, és a Velpic SAML-alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat az SAML-Velpic való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-8-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha a Velpic SAML-t manuálisan szeretné beállítani, nyisson meg egy új böngészőablakot, és jelentkezzen be a Velpic SAML vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Kattintson a **kezelés** fülre, és lépjen az **integráció** szakaszra, ahol a **bővítmények** gombra kattintva új beépülő modult hozhat létre a bejelentkezéshez.

    ![Képernyőfelvétel: az integrációs oldal, ahol kiválaszthatja a plugins elemet.](./media/velpicsaml-tutorial/velpic_1.png)

5. Kattintson a **"plugin" hozzáadása "** gombra.
    
    ![Képernyőfelvétel: a beépülő modul hozzáadása gomb van kiválasztva.](./media/velpicsaml-tutorial/velpic_2.png)

6. Kattintson az **SAML** csempére a beépülő modul hozzáadása oldalon.
    
    ![A képernyőkép a beépülő modul hozzáadása oldalon kiválasztott SAML-t jeleníti meg.](./media/velpicsaml-tutorial/velpic_3.png)

7. Adja meg az új SAML beépülő modul nevét, és kattintson a **Hozzáadás** gombra.

    ![A képernyőképen az új SAML beépülő modul hozzáadása párbeszédpanel jelenik meg, az Azure A D-vel megadva.](./media/velpicsaml-tutorial/velpic_4.png)

8. Adja meg a részleteket a következőképpen:

    ![Képernyőfelvétel: az Azure A D oldalát jeleníti meg, ahol megadhatja a leírt értékeket.](./media/velpicsaml-tutorial/velpic_5.png)

    a. A **név** szövegmezőbe írja be az SAML beépülő modul nevét.

    b. A **kiállító URL-címe** szövegmezőbe illessze be a másolt **Azure ad-azonosítót** a Azure Portal-bejelentkezési ablakának **konfigurálásához** .

    c. A **szolgáltatói metaadatok konfigurációjában** töltse fel a Azure Portalból letöltött metaadat XML-fájlt.

    d. Azt is megteheti, hogy az SAML-t csak időben engedélyezi az **"új felhasználók automatikus létrehozása"** jelölőnégyzet bejelölésével. Ha a felhasználó nem létezik a Velpic-ben, és ez a jelző nincs engedélyezve, az Azure-beli bejelentkezés sikertelen lesz. Ha a jelző engedélyezve van, akkor a rendszer automatikusan kiépíti a felhasználót a Velpic a bejelentkezéskor. 

    e. Másolja az **egyszeri bejelentkezési URL-címet** a szövegmezőből, és illessze be a Azure Portalba.
    
    f. Kattintson a **Mentés** gombra.

### <a name="create-velpic-saml-test-user"></a>Velpic SAML-teszt felhasználó létrehozása

Ez a lépés általában nem szükséges, mivel az alkalmazás csak időben támogatja a felhasználók üzembe helyezését. Ha az automatikus felhasználó-kiépítés nincs engedélyezve, a manuális felhasználói létrehozás az alább leírtak szerint végezhető el.

Jelentkezzen be a Velpic SAML-céges webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:
    
1. Kattintson a kezelés fülre, és lépjen a felhasználók szakaszra, majd kattintson az új gombra a felhasználók hozzáadásához.

    ![felhasználó hozzáadása](./media/velpicsaml-tutorial/velpic_7.png)

2. Az **"új felhasználó létrehozása"** párbeszédpanelen hajtsa végre a következő lépéseket.

    ![felhasználó!](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Az **Utónév** szövegmezőbe írja be a B utónevét.

    b. A **vezetéknév** szövegmezőbe írja be a Simon vezetéknevét.

    c. A **Felhasználónév** szövegmezőbe írja be a B. Simon nevű felhasználónevet.

    d. Az **e-mail** szövegmezőbe írja be a fiók e-mail-címét B.Simon@contoso.com .

    e. A többi adat megadása nem kötelező, szükség esetén kitöltheti.
    
    f. Kattintson a **Mentés** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

1. Ha a hozzáférési panelen a Velpic SAML csempére kattint, akkor a Velpic SAML-alkalmazás bejelentkezési lapját kell beolvasnia. A bejelentkezési oldalon a **"Bejelentkezés az Azure ad-be"** gombra kell kattintania.

    ![Képernyőfelvétel: a tanulási portál, amelyen be van jelölve az Azure A D-vel való bejelentkezés.](./media/velpicsaml-tutorial/velpic_6.png)

1. Az Azure ad-fiókkal való bejelentkezéshez kattintson a **"Bejelentkezés az Azure ad-vel"** gombra a Velpic való bejelentkezéshez.

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Velpic SAML kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)