---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Netskope-felügyeleti konzolokkal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Netskope felügyeleti konzol között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2021
ms.author: jeedes
ms.openlocfilehash: 5f07e9dbd574d8a27e6048154c72b9afc24b6ba4
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286126"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Netskope felügyeleti konzol

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Netskope-felügyeleti konzolokat Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Netskope felügyeleti konzol, a következőket teheti:

* Az Azure AD-ben a Netskope-felügyeleti konzolhoz hozzáférő vezérlő.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az Azure AD-fiókjával felügyeleti konzol Netskope.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Netskope felügyeleti konzol egyszeri bejelentkezés (SSO) engedélyezett előfizetést.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Netskope felügyeleti konzol támogatja **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Netskope-felügyeleti konzol hozzáadása a katalógusból

A Netskope felügyeleti konzol Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Netskope-felügyeleti konzol a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Netskope felügyeleti konzol** kifejezést a keresőmezőbe.
1. Válassza ki a **Netskope felügyeleti konzol** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-netskope-administrator-console"></a>Azure AD SSO konfigurálása és tesztelése a Netskope felügyeleti konzol

Konfigurálja és tesztelje az Azure AD SSO-t a Netskope felügyeleti konzol egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Netskope felügyeleti konzolban.

Az Azure AD SSO konfigurálásához és a Netskope felügyeleti konzol való teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Netskope felügyeleti konzol SSO konfigurálása](#configure-netskope-administrator-console-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Netskope felügyeleti konzol tesztelési felhasználóval](#create-netskope-administrator-console-test-user)** , hogy a B. Simon partnere legyen a Netskope felügyeleti konzol, amely a felhasználó Azure ad-beli képviseletéhez van csatolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Netskope felügyeleti konzol** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges válasz URL-címével. Az oktatóanyag későbbi részében ismertetett értéket fogja kapni.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<tenantname>.goskope.com`

    > [!NOTE]
    > A bejelentkezési URL-címek értéke nem valódi. A bejelentkezési URL-cím értékének frissítése a tényleges bejelentkezési URL-címmel. A bejelentkezési URL-cím beszerzéséhez lépjen kapcsolatba a [Netskope felügyeleti konzol ügyfél-támogatási csapatával](mailto:support@netskope.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Netskope felügyeleti konzol alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a Netskope felügyeleti konzol alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name |  Forrás attribútum|
    | ---------| --------- |
    | rendszergazda – szerepkör | User. assignedroles |

    > [!NOTE]
    > Ide [kattintva](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) megtudhatja, hogyan hozhat létre szerepköröket az Azure ad-ben.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Netskope felügyeleti konzol beállítása** szakaszban másolja a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Netskope felügyeleti konzolhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Netskope felügyeleti konzol** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a fentiekben ismertetett módon állította be a szerepköröket, kiválaszthatja a **szerepkör kiválasztása** legördülő listából.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-netskope-administrator-console-sso"></a>Netskope felügyeleti konzol SSO konfigurálása

1. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a Netskope felügyeleti konzol vállalati webhelyre rendszergazdaként.

1. Kattintson a bal oldali navigációs ablaktábla **Beállítások** fülére.

    ![Képernyőfelvétel: a navigációs ablaktáblán a kiválasztott beállítás látható.](./media/netskope-cloud-security-tutorial/configure-settings.png)

1. Kattintson az **Adminisztráció** fülre.

    ![A képernyőképen a beállítások közül kiválasztott felügyelet látható.](./media/netskope-cloud-security-tutorial/administration.png)

1. Kattintson az **SSO** fülre.

    ![A képernyőképen a felügyelet alatt kiválasztva látható S S](./media/netskope-cloud-security-tutorial/tab.png)

1. A **hálózati beállítások** szakaszban hajtsa végre a következő lépéseket:
    
    ![Képernyőfelvétel: hálózati beállítások, ahol megadhatja a leírt értékeket.](./media/netskope-cloud-security-tutorial/network.png)

    a. Másolja a **felhasználói szolgáltatás URL-címének** értékét, és illessze be a **Válasz URL-** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    b. Másolja a **szolgáltatói entitás azonosítójának** értékét, és illessze be a Azure Portal **alapszintű SAML-konfiguráció** szakaszának **azonosító** szövegmezőbe.

1. Kattintson a **beállítások szerkesztése** lehetőségre az **SSO/slo beállítások** szakaszban.

    ![Képernyőfelvétel: S S O/S L O-beállítások, ahol a beállítások szerkesztése lehetőséget választhatja.](./media/netskope-cloud-security-tutorial/settings.png)

1. A **Beállítások** előugró ablakban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a beállítások párbeszédpanel, ahol megadhatja a leírt értékeket.](./media/netskope-cloud-security-tutorial/configuration.png)

    a. Válassza az **egyszeri bejelentkezés engedélyezése** lehetőséget.

    b. A **identitásszolgáltató URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    c. Az **IDENTITÁSSZOLGÁLTATÓ entitás-azonosító** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt.

    d. Nyissa meg a letöltött Base64-kódolású tanúsítványt a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be a **identitásszolgáltató-tanúsítvány** szövegmezőbe.

    e. Válassza az **egyszeri bejelentkezés engedélyezése** lehetőséget.

    f. Illessze be a **IDENTITÁSSZOLGÁLTATÓ slo URL** szövegmezőbe a **kijelentkezési URL** értékét, amelyet a Azure Portal másolt.

    : Kattintson a **Submit (Küldés**) gombra.

### <a name="create-netskope-administrator-console-test-user"></a>Netskope létrehozása felügyeleti konzol tesztelési felhasználóval

1. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a Netskope felügyeleti konzol vállalati webhelyre rendszergazdaként.

1. Kattintson a bal oldali navigációs ablaktábla **Beállítások** fülére.

    ![A képernyőfelvételen a kiválasztott beállítások láthatók.](./media/netskope-cloud-security-tutorial/configure-settings.png)

1. Kattintson az **aktív platform** fülre.

    ![A képernyőképen a beállítások közül kiválasztott aktív platform látható.](./media/netskope-cloud-security-tutorial/user-1.png)

1. Kattintson a **felhasználók** fülre.

    ![A képernyőképen az aktív platformról kiválasztott felhasználók láthatók.](./media/netskope-cloud-security-tutorial/add-user.png)

1. Kattintson a **felhasználók hozzáadása** elemre.

    ![Képernyőfelvétel: a felhasználók párbeszédpanel, amelyen a felhasználók hozzáadása lehetőséget választhatja.](./media/netskope-cloud-security-tutorial/user-add.png)

1. Adja meg a hozzáadni kívánt felhasználó e-mail-címét, majd kattintson a **Hozzáadás** gombra.

    ![Képernyőfelvétel: felhasználók hozzáadása, ahol megadhatja a felhasználók listáját.](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Netskope felügyeleti konzol bejelentkezési URL-címre, ahol a bejelentkezési folyamatot kezdeményezheti.  

* Lépjen a Netskope felügyeleti konzol bejelentkezési URL-címére, és indítsa el innen a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Netskope-felügyeleti konzolra, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások Netskope felügyeleti konzol csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra a Netskope-felügyeleti konzol, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A felügyeleti konzol Netskope konfigurálása után a kiszűrése kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
