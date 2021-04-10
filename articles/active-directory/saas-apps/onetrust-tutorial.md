---
title: 'Oktatóanyag: Azure Active Directory integráció a OneTrust Privacy Management szoftverrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a OneTrust adatvédelmi felügyeleti szoftvere között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: f0a145b0eb9dd9dbed0927ce825a21d8f47c48ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648440"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Oktatóanyag: Azure Active Directory integráció a OneTrust Privacy Management szoftverrel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a OneTrust adatvédelmi felügyeleti szoftvereit Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a OneTrust adatvédelmi felügyeleti szoftverét, a következőket teheti:

* Az Azure AD-ben a OneTrust adatvédelmi felügyeleti szoftverhez hozzáférő vezérlő.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az adatvédelmi OneTrust az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az OneTrust Privacy Management szoftverrel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos próbaverziót.
* OneTrust adatvédelem-kezelő szoftver egyszeri bejelentkezésre alkalmas előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A OneTrust Privacy Management szoftver támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO-t

* A OneTrust adatvédelem-kezelő szoftver a felhasználók üzembe helyezését **is** támogatja

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-onetrust-privacy-management-software-from-the-gallery"></a>OneTrust adatvédelem-kezelő szoftver hozzáadása a katalógusból

A OneTrust adatvédelem-kezelő szoftver Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a OneTrust adatvédelmi felügyeleti szoftvert a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **OneTrust adatvédelmi felügyeleti szoftver** kifejezést a keresőmezőbe.
1. Válassza az **OneTrust adatvédelem-kezelő szoftver** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-onetrust-privacy-management-software"></a>Az Azure AD SSO konfigurálása és tesztelése a OneTrust Privacy Management szoftverhez

Konfigurálja és tesztelje az Azure AD SSO-t a OneTrust adatvédelmi felügyeleti szoftverrel egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a OneTrust Privacy Management szoftverben.

Az Azure AD SSO konfigurálásához és teszteléséhez a OneTrust Privacy Management szoftverrel hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[OneTrust adatvédelem-kezelő szoftver egyszeri bejelentkezésének konfigurálása](#configure-onetrust-privacy-management-software-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. A **[OneTrust adatvédelem-kezelő szoftver tesztelési felhasználójának létrehozása](#create-onetrust-privacy-management-software-test-user)** – hogy a B. Simon inOneTrust adatkezelési szoftverrel rendelkezzen, amely a felhasználó Azure ad-képviseletéhez van csatolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD SSO-t a Azure Portalban.
 
1. A Azure Portal a **OneTrust adatvédelem kezelése szoftver** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyetlen Sign-On módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet: `https://www.onetrust.com/saml2`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.onetrust.com/auth/consumerservice`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

     A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<subdomain>.onetrust.com/auth/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címmel és a bejelentkezési URL-címmel. Az értékek beszerzéséhez lépjen kapcsolatba a [OneTrust adatvédelmi szolgálatának ügyfélszolgálati csapatával](mailto:support@onetrust.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **OneTrust-kezelő szoftver beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.
1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszót.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezi a B. Simon számára az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a OneTrust adatvédelmi felügyeleti szoftverhez.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **OneTrust adatvédelem-kezelő szoftver** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **Felhasználó hozzáadása** elemet. Ezután a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza ki a felhasználók listájából a **B. Simon** elemet. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

### <a name="configure-onetrust-privacy-management-software-sso"></a>OneTrust adatvédelem-kezelő szoftver egyszeri bejelentkezésének konfigurálása

Az **OneTrust adatvédelem-kezelő szoftverre** való egyszeri bejelentkezés konfigurálásához el kell küldenie a letöltött **összevonás-metaadatokat tartalmazó XML-fájlt** és a megfelelő másolt url-címeket a Azure Portalről, hogy OneTrust az adatvédelem- [kezelő szoftvereket támogató csapatot](mailto:support@onetrust.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-onetrust-privacy-management-software-test-user"></a>OneTrust adatvédelem-kezelő szoftver tesztelési felhasználójának létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a OneTrust Privacy Management szoftverben. A OneTrust adatvédelem-kezelő szoftver az igény szerinti felhasználói üzembe helyezést is támogatja, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a OneTrust adatvédelmi felügyeleti szoftverben, akkor a hitelesítés után létrejön egy új.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [OneTrust Privacy Management ügyfélszolgálatával](mailto:support@onetrust.com).

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja az OneTrust adatvédelem-kezelő szoftver bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  
 
* Lépjen a OneTrust Privacy Management szoftver bejelentkezési URL-címére közvetlenül, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a OneTrust adatvédelmi felügyeleti szoftverbe, amelyhez be szeretné állítani az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások OneTrust adatvédelem-kezelő szoftver csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a OneTrust adatvédelmi felügyeleti szoftverbe, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A OneTrust adatvédelmi felügyeleti szoftver konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).