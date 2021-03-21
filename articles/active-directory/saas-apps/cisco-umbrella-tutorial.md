---
title: 'Oktatóanyag: Azure Active Directory integráció a Cisco Umbrella admin SSO-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Cisco Umbrella admin SSO között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 6073142b21a28f1242e0e6ec65ee2945a354b60b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592506"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella-admin-sso"></a>Oktatóanyag: Azure Active Directory integráció a Cisco Umbrella rendszergazdai SSO-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Cisco Umbrella admin SSO-t Azure Active Directory (Azure AD-val). Ha a Cisco Umbrella admin SSO-t az Azure AD-vel integrálja, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Cisco Umbrella admin SSO-hoz.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Cisco Umbrella admin SSO-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Cisco Umbrella rendszergazdai egyszeri bejelentkezéses egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Cisco Umbrella admin SSO támogatja **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.

## <a name="add-cisco-umbrella-admin-sso-from-the-gallery"></a>Cisco Umbrella admin SSO hozzáadása a katalógusból

A Cisco Umbrella admin SSO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Cisco Umbrella admin SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Cisco Umbrella admin SSO** kifejezést a keresőmezőbe.
1. Válassza a **Cisco Umbrella admin SSO** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-cisco-umbrella-admin-sso"></a>Azure AD SSO konfigurálása és tesztelése a Cisco Umbrella admin SSO-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a Cisco Umbrella admin SSO-val egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Cisco Umbrella admin SSO-ban.

Az Azure AD SSO konfigurálásához és teszteléséhez a Cisco Umbrella admin SSO használatával hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Állítsa be a Cisco Umbrella admin SSO SSO](#configure-cisco-umbrella-admin-sso-sso)** -t – az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. **[Cisco Umbrella-rendszergazdai SSO-teszt felhasználó létrehozása](#create-cisco-umbrella-admin-sso-test-user)** – a felhasználó Azure ad-beli képviseletéhez kapcsolódó B. Simon-partneri kapcsolathoz a Cisco Umbrella rendszergazdai egyszeri bejelentkezéssel.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Cisco Umbrella admin SSO** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania egy lépést, mivel az alkalmazás már előre integrálva van az Azure-ban.

    a. Ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    b. Kattintson a **további URL-címek beállítása** elemre.

    c. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet: `https://login.umbrella.com/sso`

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** lehetőségre a **metaadatok XML** -fájljának a megadott beállítások alapján történő letöltéséhez, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Cisco Umbrella admin SSO beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Cisco Umbrella admin SSO-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Cisco Umbrella admin egyszeri bejelentkezés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-cisco-umbrella-admin-sso-sso"></a>Cisco Umbrella admin SSO SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Cisco Umbrella admin SSO céges webhelyre rendszergazdaként.

2. A menü bal oldalán kattintson a **rendszergazda** elemre, és keresse meg a **hitelesítést** , majd kattintson az **SAML** elemre.

    ![A rendszergazda](./media/cisco-umbrella-tutorial/admin.png)

3. Válassza a **továbbiak** lehetőséget, majd kattintson a **tovább** gombra.

    ![A másik](./media/cisco-umbrella-tutorial/other.png)

4. A **Cisco Umbrella admin SSO metaadatok** lapon kattintson a **tovább** gombra.

    ![A metaadatok](./media/cisco-umbrella-tutorial/metadata.png)

5. Ha a **metaadatok feltöltése** lapon előre konfigurált SAML-t állított be, válassza a **kattintson ide a módosításhoz** lehetőségre, és kövesse az alábbi lépéseket.

    ![A következő](./media/cisco-umbrella-tutorial/next.png)

6. Az **a: fájl feltöltése XML-fájlban** töltse fel a Azure Portal letöltött **összevonási metaadatok XML-** fájlját, és a metaadatok feltöltése után az alábbi értékek automatikusan feltöltve értéket kapnak, majd kattintson a **tovább** gombra.

    ![A choosefile](./media/cisco-umbrella-tutorial/choose-file.png)

7. Az **SAML-konfiguráció ellenőrzése** szakaszban kattintson **az SAML-konfiguráció tesztelése** elemre.

    ![A teszt](./media/cisco-umbrella-tutorial/test.png)

8. Kattintson a **Mentés** gombra.

### <a name="create-cisco-umbrella-admin-sso-test-user"></a>Cisco Umbrella-rendszergazda SSO-teszt felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Cisco Umbrella admin SSO-ba, a Cisco Umbrella admin SSO-ba kell kiépíteni őket.  
A Cisco Umbrella rendszergazdai egyszeri bejelentkezés esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Egy másik böngészőablakban jelentkezzen be a Cisco Umbrella admin SSO céges webhelyre rendszergazdaként.

2. A menü bal oldalán kattintson a **rendszergazda** lehetőségre, és navigáljon a **fiókok** elemre.

    ![A fiók](./media/cisco-umbrella-tutorial/account.png)

3. A **fiókok** lapon kattintson az oldal jobb felső sarkában található **Hozzáadás** gombra, és végezze el a következő lépéseket.

    ![A felhasználó](./media/cisco-umbrella-tutorial/create-user.png)

    a. Az Utónév mezőben adja meg a vezetéknév (például **Britta**) **nevet** .

    b. A **vezetékneve** mezőben adja meg a (z) **Simon** nevet.

    c. A **delegált rendszergazdai szerepkör kiválasztása** lapon válassza ki a szerepkört.

    d. Az **e-mail-cím** mezőben adja meg a **brittasimon \@ contoso.com** felhasználóhoz hasonló felhasználói címet.

    e. A **jelszó** mezőbe írja be a jelszavát.

    f. A **Jelszó megerősítése** mezőbe írja be újra a jelszót.

    : Kattintson a **Létrehozás** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez a szolgáltatás átirányítja a Cisco esernyő rendszergazdai SSO bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen a Cisco Umbrella admin SSO bejelentkezési URL-címére közvetlenül, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie a Cisco UMBRELLA admin SSO-ba, amelyhez be kell állítania az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások területén a Cisco Umbrella admin egyszeri bejelentkezés csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a Cisco Umbrella admin SSO-ba, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A Cisco Umbrella admin SSO konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).