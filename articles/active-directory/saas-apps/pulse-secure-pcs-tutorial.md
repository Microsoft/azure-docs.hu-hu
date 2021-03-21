---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Pulse Secure PC-vel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Pulse Secure számítógépek között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: d38ff5c8f33bda5b12f6267e7a8cdf477db6c7d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92511443"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-pcs"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Pulse Secure PC-vel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Pulse Secure PC-ket Azure Active Directory (Azure AD) használatával. Ha a Pulse Secure PC-ket az Azure AD-vel integrálja, a következőket teheti:

* A Pulse Secure PC-k hozzáférését biztosító Azure AD-vezérlő.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával rendelkező számítógépek impulzusos védelmére.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Pulse Secure PC egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Pulse Secure-számítógépek támogatják az **SP** által KEZDEMÉNYEZett SSO

## <a name="adding-pulse-secure-pcs-from-the-gallery"></a>Pulse Secure PC-k hozzáadása a katalógusból

A Pulse Secure PC-k Azure AD-be való integrálásának konfigurálásához Pulse Secure PC-ket kell hozzáadnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **PULSe Secure PCs** kifejezést a keresőmezőbe.
1. Válassza a **Pulse Secure PC-k** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-pcs"></a>Azure AD SSO konfigurálása és tesztelése a Pulse Secure PC-ken

Konfigurálja és tesztelje az Azure AD SSO-t a Pulse Secure PC-k segítségével egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Pulse Secure PC-ken.

Az Azure AD SSO és a Pulse Secure PC-k konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Impulzusos biztonsági számítógépek egyszeri bejelentkezésének konfigurálása](#configure-pulse-secure-pcs-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Pulse Secure PC-k létrehozása](#create-pulse-secure-pcs-test-user)** – ha a felhasználó az Azure ad-hoz hasonló felhasználóhoz kapcsolódik, a "B
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Pulse Secure PC-k** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<FQDN of PCS>/dana-na/auth/saml-consumer.cgi`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<FQDN of PCS>/dana-na/auth/saml-endpoint.cgi?p=sp1`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://[FQDN of PCS]/dana-na/auth/saml-consumer.cgi`


    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, a válasz URL-címével és az azonosítóval. Vegye fel a kapcsolatot [Pulse Secure PC-k ügyfél-támogatási csapatával](mailto:support@pulsesecure.net) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Pulse Secure PC** -k beállítása szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a Pulse Secure PC-k hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Pulse Secure PC-k** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-pulse-secure-pcs-sso"></a>Impulzusos biztonságos számítógépek egyszeri bejelentkezésének konfigurálása

Ez a szakasz a számítógépek SAML SP-ként történő konfigurálásához szükséges SAML-konfigurációkat ismerteti. A többi alapkonfigurációt, például a Realms és a szerepkörök létrehozását nem fedi le a rendszer.

**A Pulse Secure-konfigurációk a következők:**

* Az Azure AD konfigurálása SAML metaadat-szolgáltatóként
* SAML Auth-kiszolgáló konfigurálása
* Hozzárendelés a megfelelő tartományokhoz és szerepkörökhöz

#### <a name="configuring-azure-ad-as-saml-metadata-provider"></a>Az Azure AD konfigurálása SAML metaadat-szolgáltatóként

Hajtsa végre a következő lépéseket a következő oldalon:

![Impulzusos kapcsolat – biztonságos konfiguráció](./media/pulse-secure-pcs-tutorial/saml-configuration.png)

1. Bejelentkezés a Pulse Secure felügyeleti konzolba
1. Navigáljon a **System-> Configuration-> SAML**
1. Kattintson az **új metaadat-szolgáltató** elemre.
1. Adja meg az érvényes nevet a **név** szövegmezőben
1. Töltse fel a letöltött metaadatok XML-fájlját a Azure Portalból az **Azure ad metaadat-fájljába**.
1. **Aláírás nélküli metaadatok fogadásának** kiválasztása
1. Szerepkörök kiválasztása **identitás-szolgáltatóként**
1. Kattintson a **módosítások mentése** gombra.

#### <a name="steps-to-create-a-saml-auth-server"></a>Az SAML Auth-kiszolgáló létrehozásának lépései:

1. Navigáljon a **hitelesítés – > hitelesítési kiszolgálók** elemre
1. Válassza az **új: SAML-kiszolgáló** elemet, és kattintson az **új kiszolgáló** elemre.

    ![Impulzusos kapcsolat biztonságos hitelesítési kiszolgálóval](./media/pulse-secure-pcs-tutorial/new-saml-server.png)

1. Hajtsa végre a következő lépéseket a beállítások lapon:

    ![Impulzusos kapcsolat biztonságos hitelesítési kiszolgáló beállításai](./media/pulse-secure-pcs-tutorial/server-settings.png)

    a. Adja meg a **kiszolgáló nevét** a szövegmezőben.

    b. Válassza ki a **SAML-verzió 2,0** -es és **konfigurációs módját** **metaadatokként**.

    c. Másolja a **biztonságos entitás-azonosító csatolása** értéket, és illessze be a Azure Portal **alapszintű SAML-konfiguráció** párbeszédpanelének **azonosító URL** mezőjébe.

    d. Válassza ki az Azure AD-entitás azonosítójának értékét az **Identity Provider entitás-azonosító legördülő listából**.

    e. Válassza az Azure AD bejelentkezési URL-cím értéket az **Identitáskezelő egyetlen Sign-On szolgáltatás URL-címe legördülő listából**.

    f. Az **egyszeri kijelentkezés** nem kötelező beállítás. Ha ez a beállítás be van jelölve, a rendszer a kijelentkezés után új hitelesítést kér. Ha ez a beállítás nincs bejelölve, és nem zárta be a böngészőt, akkor a hitelesítés nélkül újra csatlakozhat.

    : Válassza ki a **kért Authn környezeti osztályt** **jelszóként** és az **összehasonlítási metódust** a lehető **legpontosabban**.

    h. A **metaadatok érvényességének** beállítása napok száma alapján.
    
    i. Kattintson a **módosítások mentése** gombra.

### <a name="create-pulse-secure-pcs-test-user"></a>Pulse Secure PC-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Pulse Secure PC-ken. A [Pulse Secure PC-k támogatási csapatával](mailto:support@pulsesecure.net) a felhasználókat a Pulse Secure PC platformon veheti fel. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

1. Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Pulse Secure PC bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

2. Lépjen a Pulse Secure PC-k bejelentkezési URL-címére közvetlenül, és indítsa el onnan a bejelentkezési folyamatot.

3. Használhatja a Microsoft Access panelt. Ha a hozzáférési panelen a Pulse Secure számítógépek csempére kattint, a rendszer átirányítja a Pulse Secure PC bejelentkezési URL-címére. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

A Pulse Secure PC-k konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).