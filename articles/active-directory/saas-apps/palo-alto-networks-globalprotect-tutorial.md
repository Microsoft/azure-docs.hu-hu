---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Palo Alto Networks-GlobalProtect | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Palo Alto Networks-GlobalProtect között.
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
ms.openlocfilehash: 72072feebfcf8dba249d2045a399e09714177698
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963689"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Palo Alto Networks-GlobalProtect

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Palo Alto Networks-GlobalProtect Azure Active Directory (Azure AD) szolgáltatással. A Palo Alto Networks-GlobalProtect és az Azure AD integrálásával a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Palo Alto Networks-GlobalProtect-hez.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezzenek a Palo Alto Networks-GlobalProtect az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Palo Alto-hálózatok – GlobalProtect egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Palo Alto hálózatok – a GlobalProtect támogatja az **SP** által kezdeményezett egyszeri bejelentkezést
* Palo Alto Networks – **a GlobalProtect a** felhasználók üzembe helyezését támogatja

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Palo Alto-hálózatok hozzáadása – GlobalProtect a katalógusból

A Palo Alto Networks-GlobalProtect az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia Palo Alto Networks-GlobalProtect a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **Palo Alto Networks-GlobalProtect** a keresőmezőbe.
1. Válassza a **Palo Alto Networks-GlobalProtect** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-palo-alto-networks---globalprotect"></a>Azure AD SSO konfigurálása és tesztelése a Palo Alto Networks-GlobalProtect

Azure AD SSO konfigurálása és tesztelése a Palo Alto Networks használatával – GlobalProtect egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Palo Alto Networks-GlobalProtect.

Az Azure AD SSO konfigurálásához és teszteléséhez a Palo Alto Networks-GlobalProtect használatával hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Palo Alto Networks-GLOBALPROTECT SSO konfigurálása](#configure-palo-alto-networks---globalprotect-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. A **[Palo Alto Networks-GlobalProtect tesztelési felhasználó létrehozása](#create-palo-alto-networks---globalprotect-test-user)** – ha B. Simon-t szeretne a Palo Alto Networks-GlobalProtect, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Palo Alto Networks-GlobalProtect** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<Customer Firewall URL>`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Vegye fel a kapcsolatot a [Palo Alto Networks-GlobalProtect ügyfél-támogatási csapatával](https://support.paloaltonetworks.com/support) , és szerezze be ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Palo Alto Networks-GlobalProtect beállítása** szakaszban másolja ki a megfelelő URL-címeket (ka) t a követelmény alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a Palo Alto Networks-GlobalProtect elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Palo Alto Networks-GlobalProtect** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>A Palo Alto-hálózatok konfigurálása – GlobalProtect SSO

1. Nyissa meg a Palo Alto Networks-GlobalProtect rendszergazdaként egy másik böngészőablakban.

2. Kattintson az **eszköz** elemre.

    ![A Palo Alto egyszeri bejelentkezés konfigurálása 1](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Válassza az **SAML-azonosító** lehetőséget a bal oldali navigációs sávon, és kattintson az importálás lehetőségre a metaadat-fájl importálásához.

    ![A Palo Alto egyszeri bejelentkezés konfigurálása 2](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Hajtsa végre a következő műveleteket az importálási ablakban

    ![A Palo Alto egyszeri bejelentkezés konfigurálása 3](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. A **profil neve** szövegmezőben adjon meg egy nevet, például: Azure ad GlobalProtect.

    b. Az **Identitáskezelő metaadatainak** területén kattintson a **Tallózás** gombra, és válassza ki azt a metadata.xml fájlt, amelyet letöltött Azure Portal

    c. Kattintson **az OK** gombra

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Palo Alto-hálózatok létrehozása – GlobalProtect teszt felhasználó

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Palo Alto Networks-GlobalProtect. Palo Alto Networks – a GlobalProtect támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a Palo Alto Networks-GlobalProtect-ben, a hitelesítés után létrejön egy újat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a Palo Alto Networks-GlobalProtect bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

* Nyissa meg a Palo Alto Networks-GlobalProtect bejelentkezési URL-címét, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Palo Alto Networks-GlobalProtect csempére kattint, automatikusan be kell jelentkeznie a Palo Alto Networks-GlobalProtect, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A Palo Alto Networks-GlobalProtect konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).