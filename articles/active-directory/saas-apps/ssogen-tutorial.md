---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SSOGEN-vel – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SSOGEN között – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE.
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
ms.openlocfilehash: 9eeafaf0f5fbfaff9394ced0a0623f2fb462ed4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646994"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SSOGEN-vel – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a SSOGEN-Azure AD SSO-átjárót az Oracle E-Business Suite-EBS, a PeopleSoft és a JDE Azure Active Directory (Azure AD) használatával. Ha integrálja a SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE az Azure AD-vel, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a SSOGEN-hez – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite – az EBS, a PeopleSoft és a JDE támogatja az **SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>SSOGEN hozzáadása – Azure AD SSO Gateway az Oracle E-Business Suite-EBS, PeopleSoft és JDE a katalógusból

A SSOGEN-Azure AD SSO-átjáró az Oracle E-Business Suite-EBS, a PeopleSoft és a JDE Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a SSOGEN-Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **SSOGEN-Azure ad SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE** kifejezést a keresőmezőbe.
1. Válassza a **SSOGEN – Azure ad SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Azure AD SSO konfigurálása és tesztelése SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE

Az Azure AD SSO konfigurálása és tesztelése a SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a SSOGEN-ben – Azure AD SSO Gateway az Oracle E-Business Suite-EBS, PeopleSoft és JDE esetében.

Az Azure AD SSO konfigurálásához és teszteléséhez a SSOGEN-Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE esetén hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SSOGEN konfigurálása – Azure ad SSO-átjáró az Oracle E-Business Suite-EBS, PeopleSoft és JDE egyszeri](#configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso)** bejelentkezéshez – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[SSOGEN létrehozása – Azure ad SSO-átjáró a következőhöz: Oracle e-Business Suite-EBS, PeopleSoft és JDE test User](#create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user)** – a B. Simon-nek a SSOGEN – Azure ad SSO Gateway for Oracle e-Business Suite-EBS, PEOPLESOFT és JDE, amely a felhasználó Azure ad-képviseletéhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **SSOGEN – Azure ad SSO-átjáró az Oracle E-Business Suite-EBS, a PeopleSoft és a JDE alkalmazás-** integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címével és Sign-On URL-címmel. Vegye fel a kapcsolatot a [SSOGEN-vel – Azure ad SSO-átjáró az Oracle E-Business Suite-EBS, PeopleSoft és JDE ügyfél-támogatási csapatának](mailto:support@ssogen.com) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A SSOGEN-Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE alkalmazás meghatározott formátumban várja az SAML-jogcímeket, amelyhez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName** leképezéssel van leképezve. SSOGEN – az Azure AD SSO Gateway for Oracle E-Business Suite-EBS, a PeopleSoft és a JDE alkalmazás a **NameIdentifier** -t a **User. onpremisessamaccountname**-vel rendeli hozzá, ezért az attribútum-hozzárendelés szerkesztéséhez kattintson a **Szerkesztés** ikonra, és módosítsa az attribútumok leképezését.

    ![image](common/edit-attribute.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **SSOGEN-Azure ad SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso"></a>SSOGEN konfigurálása – Azure AD SSO-átjáró az Oracle E-Business Suite-EBS, PeopleSoft és JDE SSO-hoz

Az egyszeri bejelentkezés konfigurálása a **SSOGEN-ben – Azure ad SSO-átjáró az Oracle E-Business Suite-EBS, a PeopleSoft és a JDE oldalon az** alkalmazás-specifikus SSO regisztrációs dokumentációjában olvasható

* Oracle EBS – Azure AD SSO-integráció: [https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* PeopleSoft – Azure AD SSO-integráció: [https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards – Azure AD SSO-integráció: [https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Apache – Azure AD SSO-integráció: [https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user"></a>SSOGEN létrehozása – Azure AD SSO-átjáró az Oracle E-Business Suite-EBS, PeopleSoft és JDE test User számára

Az Azure AD a hitelesítés sikeres elvégzése után egyedi felhasználói azonosítót (Name ID) küld a felhasználói alkalmazásnak.  Győződjön meg arról, hogy az egyedi felhasználói azonosító (Name ID) megfelel az alkalmazásban lévő felhasználói rekordnak, FND_USER. USER_NAME például az Oracle EBS-ben.

Forduljon [info@ssogen.com](mailto:info@ssogen.com) [support@ssogen.com](mailto:support@ssogen.com) a támogatási szolgálathoz, és kérjen segítséget.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a SSOGEN – Azure AD SSO-átjárót az Oracle E-Business Suite-EBS, PeopleSoft és JDE bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Nyissa meg a SSOGEN-Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE bejelentkezési URL-címet, és indítsa el innen a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie a SSOGEN-Azure ad SSO Gateway for Oracle E-Business Suite-EBS, PEOPLESOFT és JDE számára, amelyhez be kell állítania az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha rákattint a SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, a PeopleSoft és a JDE csempe a saját alkalmazásokban, ha SP módban van konfigurálva, a bejelentkezési folyamat elindításához és a IDENTITÁSSZOLGÁLTATÓ módban való konfigurálásához a rendszer automatikusan átirányítja az alkalmazás bejelentkezési lapjára, és ha az az Oracle E-Business Suite-EBS-re van konfigurálva, automatikusan be kell jelentkeznie a SSOGEN-Azure AD SSO-átjáróba. , PeopleSoft és JDE, amelyekhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a SSOGEN – Azure AD SSO-átjárót az Oracle E-Business Suite-EBS, a PeopleSoft és a JDE számára, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).