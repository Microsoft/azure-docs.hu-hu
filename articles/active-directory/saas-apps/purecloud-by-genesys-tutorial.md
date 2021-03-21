---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a PureCloud-mel a Genesys használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést az Azure Active Directory és a PureCloud között a Genesys használatával.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 7c6f84ee3bb4920dbe57221b8b0bbf9f5880742b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653005"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a PureCloud-mel a Genesys használatával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a PureCloud a Genesys segítségével a Azure Active Directory (Azure AD) használatával. Ha a Genesys és az Azure AD segítségével integrálja a PureCloud-et, a következőket teheti:

* Vezérlés az Azure AD-ben, aki a Genesys által PureCloud fér hozzá.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a PureCloud az Azure AD-fiókjával való Genesys.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik ilyennel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* PureCloud Genesys egyszeri bejelentkezéssel (SSO) – engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Genesys által támogatott PureCloud az **SP és a identitásszolgáltató**– kezdeményezett SSO-t támogatja.

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-purecloud-by-genesys-from-the-gallery"></a>PureCloud hozzáadása a Genesys a gyűjteményből

Ahhoz, hogy az Azure AD-be Genesys az PureCloud-integrációt, hozzá kell adnia a PureCloud a Genesys a katalógusból a felügyelt SaaS-alkalmazások listájához. Ehhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **PureCloud by Genesys** kifejezést a keresőmezőbe.
1. Az eredmények panelen válassza a **PureCloud Genesys alapján** lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-purecloud-by-genesys"></a>Az Azure AD SSO konfigurálása és tesztelése a PureCloud Genesys

Konfigurálja és tesztelje az Azure AD SSO-t a PureCloud-mel a Genesys használatával egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a PureCloud által a Genesys.

Az Azure AD SSO konfigurálásához és a Genesys-mel történő PureCloud való teszteléséhez hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Konfigurálja a PureCloud a GENESYS SSO használatával](#configure-purecloud-by-genesys-sso)** az alkalmazás oldalán lévő egyszeri bejelentkezés beállításainak konfigurálásához.
    1. **[Hozzon létre PureCloud a Genesys tesztelési felhasználóval](#create-purecloud-by-genesys-test-user)** , hogy az a felhasználó Azure ad-beli képviseletéhez kapcsolódó, B. Simon PureCloud.
1. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ha engedélyezni szeretné az Azure AD SSO használatát a Azure Portalban, kövesse az alábbi lépéseket:

1. A Azure Portal **PureCloud a Genesys** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyetlen Sign-On módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha az **alapszintű SAML-konfiguráció** szakaszban szeretné konfigurálni az alkalmazást **identitásszolgáltató** módban, adja meg a következő mezők értékeit:

    a. Az **azonosító** mezőben adja meg a régiójának megfelelő URL-címeket:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.au/saml
    ```

    b. A **Válasz URL-címe** mezőbe írja be a régiójának megfelelő URL-címeket:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.com.au/saml
    ```

1. Válassza a **további URL-címek beállítása** lehetőséget, majd a következő lépéssel konfigurálja az alkalmazást **SP** -ben kezdeményezett módban:

    A **bejelentkezési URL-cím** mezőbe írja be a régiójának megfelelő URL-címeket:
    
    ```http
    https://login.mypurecloud.com
    https://login.mypurecloud.de
    https://login.mypurecloud.jp
    https://login.mypurecloud.ie
    https://login.mypurecloud.com.au
    ```

1. A Genesys-alkalmazás által használt PureCloud egy adott formátumban várja az SAML-jogcímeket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvétel az alapértelmezett attribútumok listáját jeleníti meg:

    ![image](common/default-attributes.png)

1. Emellett a Genesys alkalmazás által készített PureCloud néhány további attribútumot vár az SAML-válaszban, ahogy az a következő táblázatban látható. Ezek az attribútumok előre is fel vannak töltve, de szükség szerint áttekinthetők.

    | Name | Forrás attribútum|
    | ---------------| --------------- |
    | E-mail | User. userPrincipalName |
    | OrganizationName | `Your organization name` |

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **PureCloud által Genesys beállítása** szakaszban másolja a megfelelő URL-címet (vagy URL-címeket) a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portalban:

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a felhasználónevet a következő formátumban: username@companydomain.extension . Példa: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd jegyezze fel a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a PureCloud számára a Genesys.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **PureCloud a Genesys alapján** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-purecloud-by-genesys-sso"></a>PureCloud konfigurálása Genesys SSO-val

1. Egy másik böngészőablakban jelentkezzen be a PureCloud Genesys rendszergazdaként.

1. Válassza a felső **rendszergazda** lehetőséget, majd az **integrációk** területen lépjen az **egyszeri bejelentkezés** lehetőségre.

    ![Képernyőfelvétel: a PureCloud felügyeleti ablaka, amelyen kiválaszthatja az egyszeri bejelentkezés lehetőséget.](./media/purecloud-by-genesys-tutorial/configure-1.png)

1. Váltson az **ADFS/Azure ad (prémium)** lapra, majd kövesse az alábbi lépéseket:

    ![Képernyőfelvétel: az integrációs oldal, ahol megadhatja a leírt értékeket.](./media/purecloud-by-genesys-tutorial/configure-2.png)

    a. A **Tallózás** gombra kattintva töltse fel a Azure Portalból letöltött base-64 kódolt tanúsítványt az **ADFS-tanúsítványba**.

    b. Az **ADFS kiállítói URI-ja** mezőben illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    c. A **cél URI** mezőben illessze be a Azure Portalból másolt **bejelentkezési URL-cím** értékét.

    d. A **függő entitás azonosítójának** értékeként nyissa meg a Azure Portal, majd az **PureCloud by Genesys** Application Integration lapon válassza a **Tulajdonságok** lapot, és másolja az **alkalmazás-azonosító** értékét. Illessze be a **függő entitás azonosító** mezőjébe.

    ![A képernyőképen a Tulajdonságok panel látható, ahol megtalálhatja az alkalmazás I D értékét.](./media/purecloud-by-genesys-tutorial/configure-6.png)

    e. Kattintson a **Mentés** gombra.

### <a name="create-purecloud-by-genesys-test-user"></a>PureCloud létrehozása a Genesys test User használatával

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a PureCloud a Genesys-be, azokat a Genesys PureCloud kell kiépíteni. A Genesys által PureCloud a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez kövesse az alábbi lépéseket:**

1. Jelentkezzen be a PureCloud a Genesys rendszergazdaként.

1. Válassza a **rendszergazda** lehetőséget a felső részen, és lépjen a személyek személyek **& engedélyek** **területen.**

    ![Képernyőfelvétel: a PureCloud-rendszergazda ablak, ahol kiválaszthatja a kívánt személyeket.](./media/purecloud-by-genesys-tutorial/configure-3.png)

1. A **személyek** lapon válassza a **személy felvétele** lehetőséget.

    ![Képernyőfelvétel: a személyek oldal, ahol hozzáadhat egy személyt.](./media/purecloud-by-genesys-tutorial/configure-4.png)

1. A **személyek hozzáadása a szervezethez** párbeszédpanelen kövesse az alábbi lépéseket:

    ![Képernyőfelvétel: az a lap, amelyen megadhatja a leírt értékeket.](./media/purecloud-by-genesys-tutorial/configure-5.png)

    a. A **teljes név** mezőben adja meg a felhasználó nevét. Például: **B. Simon**.

    b. Az **e-mail** mezőbe írja be a felhasználó e-mail-címét. Például: **b. simon \@ contoso.com**.

    c. Válassza a **Létrehozás** lehetőséget.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a PureCloud a Genesys bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen a PureCloudra közvetlenül a Genesys bejelentkezési URL-címével, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie a PureCloud olyan Genesys, amelyhez be szeretné állítani az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások Genesys csempéje alapján kattint a PureCloud, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a PureCloud olyan Genesys, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

Ha a Genesys PureCloud konfigurálja, akkor kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).