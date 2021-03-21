---
title: 'Oktatóanyag: Azure Active Directory integráció a Ivanti-Service Managerokkal (ISM) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Ivanti Service Manager (ISM) között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 16aceeddcba574953ac03ccf04d099762b0000c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648883"
---
# <a name="tutorial-azure-active-directory-integration-with-ivanti-service-manager-ism"></a>Oktatóanyag: Azure Active Directory integráció a Ivanti-Service Managerokkal (ISM)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Ivanti Service Manager (ISM) Azure Active Directory (Azure AD) szolgáltatással. Ha az Azure AD-vel integrálja a Ivanti Service Manager (ISM), a következőket teheti:

* A Ivanti Service Manager (ISM) szolgáltatáshoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Ivanti Service Manager (ISM) szolgáltatásba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Ivanti Service Manager (ISM) egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Ivanti Service Manager (ISM) támogatja **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A Ivanti Service Manager (ISM) **a** felhasználók üzembe helyezését támogatja

## <a name="add-ivanti-service-manager-ism-from-the-gallery"></a>Ivanti-Service Manager (ISM) hozzáadása a gyűjteményből

A Ivanti Service Manager (ISM) Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a katalógusból a Ivanti-Service Manager (ISM) a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe a **IVANTI Service Manager (ISM)** kifejezést.
1. Válassza az **Ivanti Service Manager (ISM)** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-ivanti-service-manager-ism"></a>Azure AD SSO konfigurálása és tesztelése Ivanti-Service Manager (ISM)

Konfigurálja és tesztelje az Azure AD SSO-t a Ivanti Service Manager (ISM) használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Ivanti Service Manager (ISM) szolgáltatásban.

Az Azure AD SSO konfigurálásához és teszteléséhez a Ivanti Service Manager (ISM) használatával hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[Ivanti Service Manager (ISM) egyszeri bejelentkezés konfigurálása](#configure-ivanti-service-manager-ism-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy Ivanti Service Manager (ISM) tesztelési felhasználót](#create-ivanti-service-manager-ism-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-beli Ivanti Service Manager (ISM) rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Ivanti-Service Manager (ISM)** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minták egyikének használatával:
    
    ```http
    https://<customer>.saasit.com/
    https://<customer>.saasiteu.com/
    https://<customer>.saasitau.com/
    ```

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<customer>/handlers/sso/SamlAssertionConsumerHandler.ashx`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<customer>.saasit.com/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek megszerzéséhez lépjen kapcsolatba a [Ivanti Service Manager (ISM) ügyfél-támogatási csapatával](https://www.ivanti.com/support/contact) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (RAW)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

7. A **Ivanti Service Manager (ISM) beállítása** szakaszban másolja a megfelelő URL-címet (ka) t a követelmény szerint.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Ivanti Service Manager (ISM) szolgáltatáshoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Ivanti Service Manager (ISM)** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-ivanti-service-manager-ism-sso"></a>Ivanti Service Manager (ISM) SSO konfigurálása

Ha az egyszeri bejelentkezést a **Ivanti Service Manager (ISM)** oldalon szeretné konfigurálni, el kell küldenie a letöltött **tanúsítványt (RAW)** és a megfelelő másolt url-címeket a Azure Portal [Ivanti Service Manager (ISM) támogatási csapatának](https://www.ivanti.com/support/contact). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-ivanti-service-manager-ism-test-user"></a>Ivanti-Service Manager (ISM) tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Ivanti Service Manager (ISM) szolgáltatásban. A Ivanti Service Manager (ISM) támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a Ivanti Service Managerban (ISM), a rendszer egy újat hoz létre a hitelesítés után.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Ivanti Service Manager (ISM) támogatási csoporthoz](https://www.ivanti.com/support/contact).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Ivanti Service Manager (ISM) bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen a Ivanti Service Manager (ISM) bejelentkezési URL-címére közvetlenül, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Ivanti-Service Manager (ISM), amelyhez be szeretné állítani az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások Ivanti Service Manager (ISM) csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a Ivanti-Service Manager (ISM), amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A Ivanti Service Manager (ISM) konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).