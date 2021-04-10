---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Broadcom DX SaaS-vel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Broadcom DX SaaS között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2021
ms.author: jeedes
ms.openlocfilehash: 35ab4d335560689a7e55bf1a8dff9fcb0bcc7d7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601516"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-broadcom-dx-saas"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Broadcom DX SaaS-vel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Broadcom DX SaaS-t Azure Active Directory (Azure AD-val). Ha integrálja a Broadcom DX SaaS-t az Azure AD-vel, a következőket teheti:

* A Broadcom DX SaaS-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy az Azure AD-fiókjával automatikusan bejelentkezzenek az Broadcom DX SaaS-ba.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Broadcom DX SaaS egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Broadcom DX SaaS támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.

* A Broadcom DX SaaS **csak időben támogatja a** felhasználók üzembe helyezését.

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-broadcom-dx-saas-from-the-gallery"></a>Broadcom DX SaaS hozzáadása a katalógusból

A Broadcom DX SaaS az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Broadcom DX SaaS-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Broadcom DX SaaS** kifejezést a keresőmezőbe.
1. Válassza ki a **BROADCOM DX SaaS** -t az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-broadcom-dx-saas"></a>Az Azure AD SSO konfigurálása és tesztelése a Broadcom DX SaaS-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Broadcom DX SaaS használatával egy **B. Simon** nevű tesztelési felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Broadcom DX SaaS-ben.

Az Azure AD SSO és a Broadcom DX SaaS konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[BROADCOM DX SaaS SSO konfigurálása](#configure-broadcom-dx-saas-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[BROADCOM DX SaaS-teszt felhasználó létrehozása](#create-broadcom-dx-saas-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó, B. Simon-beli partnert szeretne csatolni a Broadcom DX SaaS-hez.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **BROADCOM DX SaaS** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `DXI_<TENANT_NAME>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://axa.dxi-na1.saas.broadcom.com/ess/authn/<TENANT_NAME>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Ezekhez az értékekhez forduljon a [BROADCOM DX SaaS-ügyfél támogatási csapatához](mailto:dxi-na1@saas.broadcom.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Broadcom DX SaaS-alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Broadcom DX SaaS-alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name |  Forrás attribútum|
    | --------------- | --------- |
    | Group | User. groups |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **BROADCOM DX SaaS beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a Broadcom DX SaaS elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **BROADCOM DX SaaS** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a &quot;default Access&quot; szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name=&quot;configure-broadcom-dx-saas-sso&quot;></a>Broadcom DX SaaS SSO konfigurálása

1. Jelentkezzen be a Broadcom DX SaaS-céges webhelyre rendszergazdaként.

2. Lépjen a **Beállítások** lapra, és kattintson a **felhasználók** fülre.

    ![Felhasználók](./media/broadcom-dx-saas-tutorial/settings.png &quot;Felhasználók")

3. A **felhasználói kezelés** szakaszban kattintson az ellipszisek elemre, majd válassza az **SAML** lehetőséget.

    ![Felhasználókezelés](./media/broadcom-dx-saas-tutorial/local.png "Felhasználókezelés")

4. Az **SAML-fiók azonosítása** szakaszban hajtsa végre a következő lépéseket.
   
    ![Fiók](./media/broadcom-dx-saas-tutorial/broadcom-1.png "Fiók") 

    a. A **kiállító** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    b. Illessze be a Azure Portalból másolt **bejelentkezési URL-címet** az **Identity Provider (IDENTITÁSSZOLGÁLTATÓ) bejelentkezési URL** szövegmezőbe.

    c. Az **Identity Provider (identitásszolgáltató) kijelentkezési URL-címe** szövegmezőbe illessze be azt a **KIjelentkezési URL-címet** , amelyet a Azure Portal másolt.

    d. Nyissa meg a letöltött **tanúsítványt (Base64)** a Azure Portal a Jegyzettömbben, és illessze be a tartalmat az **Identity Provider tanúsítvány** szövegmezőbe.

    e. Kattintson a **tovább** gombra.

5. A **Térkép attribútumai** szakaszban adja meg a szükséges SAML-attribútumokat a következő lapon, majd kattintson a **tovább** gombra.

    ![Attribútumok](./media/broadcom-dx-saas-tutorial/broadcom-2.png "Attribútumok") 


6. A **felhasználói csoport azonosítása** szakaszban adja meg a csoport azonosítóját, és kattintson a **tovább** gombra.

    ![Csoport hozzáadása](./media/broadcom-dx-saas-tutorial/broadcom-3.png "Csoport hozzáadása") 

7. Az **SAML-fiók feltöltése** szakaszban ellenőrizze a feltöltött értékeket, majd kattintson a **Mentés** gombra.

    ![SAML-fiók](./media/broadcom-dx-saas-tutorial/broadcom-4.png "SAML-fiók") 

### <a name="create-broadcom-dx-saas-test-user"></a>Broadcom DX SaaS-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Broadcom DX SaaS-ben. A Broadcom DX SaaS támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a Broadcom DX SaaS-ben, akkor a hitelesítés után létrejön egy újat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie a Broadcom DX SaaS-be, amelyhez be kell állítania az egyszeri bejelentkezést.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások szolgáltatásban a Broadcom DX SaaS csempére kattint, automatikusan be kell jelentkeznie a Broadcom DX SaaS-be, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

Ha a Broadcom DX SaaS-t konfigurálta, akkor kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).