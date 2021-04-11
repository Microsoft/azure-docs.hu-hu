---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a sarokkövek egyetlen Sign-Onával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Sarokköves egyszeri bejelentkezés között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/09/2021
ms.author: jeedes
ms.openlocfilehash: f7167df523ca6f84eacd92fc7af1011e8b3b00b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950335"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-single-sign-on"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Sarokköves egyetlen Sign-On

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az egyes alapSign-Onokat Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja az egyes Sign-On sarokköveit, a következőket teheti:

* Az Azure AD-ben való hozzáférés, amely hozzáférést biztosít a sarokkövei egyszeri bejelentkezéshez.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az egyetlen Sign-Onba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* EgySign-Onos egyszeri bejelentkezési (SSO) engedélyezéses előfizetés.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A sarokköve egy Sign-On támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.
* A sarokköve egy Sign-On támogatja az [automatikus felhasználó-kiépítés](cornerstone-ondemand-provisioning-tutorial.md)használatát.


## <a name="adding-cornerstone-single-sign-on-from-the-gallery"></a>Alapköve egyetlen Sign-On hozzáadása a katalógusból

Ahhoz, hogy az egyes Sign-On sarokköveit az Azure AD-be konfigurálja, hozzá kell adnia egy alapköve-Sign-On a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **sarokköve az egyszeri bejelentkezés** a keresőmezőbe.
1. Válassza ki a **sarokköves egyszeri bejelentkezés** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-cornerstone-single-sign-on"></a>Az Azure AD SSO konfigurálása és tesztelése az alapkonfigurációhoz Sign-On

Konfigurálja és tesztelje az Azure AD SSO-t a sarokköve egyetlen Sign-On egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az egyszeri bejelentkezésben.

Az Azure AD SSO konfigurálásához és a Sarokköves egyszeri bejelentkezéssel történő teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. Az **[egySign-Onos egyszeri bejelentkezés konfigurálása](#configure-cornerstone-single-sign-on-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy egyszerű Sign-On tesztelési felhasználót](#create-cornerstone-single-sign-on-test-user)** –, hogy a B. Simon egy, a felhasználó Azure ad-képviseletéhez kapcsolódó, egyetlen Sign-On-beli partnere legyen.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **sarokköve egyszeri bejelentkezés** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<PORTAL_NAME>.csod.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    c. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címmel, azonosítóval és bejelentkezési URL-címmel. Az értékek megszerzéséhez vegye fel a kapcsolatot az [egyetlen Sign-On ügyfél-támogatási csapattal](mailto:moreinfo@csod.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **sarokkövei egyszeri bejelentkezés beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a sarokkövei egyszeri bejelentkezéshez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza ki a **sarokköves egyszeri bejelentkezés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-cornerstone-single-sign-on-sso"></a>A Sarokköves egyszeri Sign-On egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be a sarokköve egyetlen Sign-On rendszergazdaként.

1. Nyissa meg a **rendszergazdai > eszközöket**.

    ![screeenshot a rendszergazda számára lapon.](./media/cornerstone-ondemand-tutorial/admin.png)

1. Válassza ki a **konfigurációs eszközök** **peremhálózati** paneljét.

    ![screeenshot az EDGE panelhez.](./media/cornerstone-ondemand-tutorial/edge-panel.png)

1. Válasszon ki egy Sign-On az **integráció** szakaszban.

    ![screeenshot egyetlen Sign-On lehetőséggel.](./media/cornerstone-ondemand-tutorial/single-sign-on.png)

1. Kattintson az **egyszeri bejelentkezés hozzáadása** gombra. Válassza a **bejövő SAML** lehetőséget az alábbi látható felugró ablakban, majd kattintson a **Hozzáadás** gombra.

    ![screeenshot a bejövő SAML-hoz.](./media/cornerstone-ondemand-tutorial/inbound.png)

1. Hajtsa végre az alábbi lépéseket a következő oldalon:

    ![screeenshot az alapkonfiguráció szakaszhoz.](./media/cornerstone-ondemand-tutorial/configuration.png)

    a. Az **Általános tulajdonságok** területen kattintson a **fájl feltöltése** gombra a **tanúsítvány (Base64)** fájljának feltöltéséhez, amelyet a Azure Portal letöltött.

    b. Jelölje be az **Engedélyezés** jelölőnégyzetet, és a **identitásszolgáltató URL-címe** szövegmezőbe illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    c. Kattintson a **Mentés** gombra.

### <a name="create-cornerstone-single-sign-on-test-user"></a>Egyedi Sign-On tesztelési felhasználó létrehozása

Ennek a szakasznak a célja, hogy egy B. Simon nevű felhasználót hozzon létre a Sarokköves egyszeri bejelentkezésben. Az egyszerű Sign-On támogatja az automatikus felhasználó-kiépítés használatát, amely alapértelmezés szerint engedélyezve van. További részletekért tekintse [meg az automatikus](./cornerstone-ondemand-provisioning-tutorial.md) felhasználó-kiépítés konfigurálását ismertető témakört.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a sarokköve egyetlen Sign-On rendszergazdaként.

1. Nyissa meg a **rendszergazda-> felhasználókat** , és kattintson a lap alján található **felhasználó hozzáadása** elemre.

    ![screeenshot a sarokkövek teszteléséhez.](./media/cornerstone-ondemand-tutorial/user-1.png)

1. Töltse ki a kötelező mezőket az **új felhasználó hozzáadása** oldalon, és kattintson a **Save (Mentés**) gombra.

    ![screeenshot a kötelező mezőkkel rendelkező felhasználók létrehozásához.](./media/cornerstone-ondemand-tutorial/user-2.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a központi Sign-On bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen az egySign-Onos bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások alapSign-On csempére kattint, a rendszer átirányítja az egy Sign-On bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta az alapkonfigurációt, Sign-On kényszerítheti a munkamenet-vezérlést, amely valós idejű védelmet biztosít a szervezet bizalmas adatai kiszűrése és beszivárgásának. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)