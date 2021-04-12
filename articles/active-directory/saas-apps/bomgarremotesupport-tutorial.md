---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a BeyondTrust távoli támogatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és BeyondTrust távoli támogatás között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 1996024d163a4bf7cfa741110038bb8db5b883e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632744"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a BeyondTrust távoli támogatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a BeyondTrust távoli támogatását Azure Active Directory (Azure AD) használatával. Ha a BeyondTrust távoli támogatását az Azure AD-vel integrálja, a következőket teheti:

* A BeyondTrust távoli támogatáshoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek, hogy BeyondTrust a távoli támogatást az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* BeyondTrust távoli támogatás egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A BeyondTrust távoli támogatása támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A BeyondTrust-támogatás **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>BeyondTrust távoli támogatásának hozzáadása a katalógusból

A BeyondTrust távoli támogatásának az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a BeyondTrust távoli támogatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **BeyondTrust távoli támogatás** kifejezést a keresőmezőbe.
1. Válassza a **BeyondTrust távoli támogatás** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-beyondtrust-remote-support"></a>Azure AD SSO konfigurálása és tesztelése BeyondTrust távoli támogatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t BeyondTrust távoli támogatással a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a BeyondTrust-távoli támogatásban.

Az Azure AD SSO és a BeyondTrust távoli támogatásának konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[BeyondTrust távoli támogatásának egyszeri bejelentkezéses](#configure-beyondtrust-remote-support-sso)** beállítása – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre BeyondTrust-alapú távoli támogatási teszt felhasználót](#create-beyondtrust-remote-support-test-user)** – ha a BeyondTrust-hez tartozó, a felhasználó Azure ad-képviseletéhez kapcsolódó távoli támogatásban található B. Simon partnere.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **BeyondTrust távoli támogatás** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával: `https://<HOSTNAME>.bomgar.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<HOSTNAME>.bomgar.com/saml/sso`
    
    c. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<HOSTNAME>.bomgar.com/saml`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a Sign-On URL-címmel. Ezeket az értékeket az oktatóanyag későbbi részében ismertetjük.

1. A BeyondTrust távoli támogatási alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a BeyondTrust távoli támogatási alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name |  Forrás attribútum|
    | ---------------| ----------|
    | Felhasználónév | User. userPrincipalName |
    | FirstName | User. givenName |
    | LastName | felhasználó. vezetéknév |
    | E-mail | User. mail |
    | Csoportok | User. groups |

    > [!NOTE]
    > Ha Azure AD-csoportokat rendel a BeyondTrust távoli támogatási alkalmazáshoz, a "jogcímek visszaadott csoportok" beállítást kell módosítania a nincs értékről a SecurityGroup. A csoportok az alkalmazásba lesznek importálva az objektum-azonosítók szerint. Az Azure AD-csoport objektumazonosító az Azure Active Directory felületen található tulajdonságok ellenőrzésével érhető el. Erre szükség lesz az Azure AD-csoportok megfelelő csoportházirendekhez való hivatkozásához és hozzárendeléséhez.

1. Az egyedi felhasználói azonosító beállításakor ezt az értéket NameID: **állandó** értékre kell beállítani. A felhasználónak a megfelelő csoportházirendek helyes azonosításához és az engedélyekhez való hozzárendeléséhez szükség van egy állandó azonosítóra. A Szerkesztés ikonra kattintva megnyithatja a **felhasználói attribútumok & jogcímek** párbeszédpanelt az egyedi felhasználói azonosító értékének szerkesztéséhez.

1. A **jogcím kezelése** szakaszban kattintson a **név azonosítójának kiválasztása** lehetőségre, és állítsa az értéket **állandó** értékre, majd kattintson a **Mentés** gombra.

    ![Felhasználói attribútumok és jogcímek](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A BeyondTrust-alapú **távoli támogatás beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a BeyondTrust távoli támogatásához.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **BeyondTrust távoli támogatás** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-beyondtrust-remote-support-sso"></a>A BeyondTrust távoli támogatásának egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a BeyondTrust-rendszergazdaként a távoli támogatás szolgáltatásba.

1. Navigáljon a **felhasználók & biztonsági**  >  **biztonsági szolgáltatók** elemre.

1. Kattintson az **SAML-szolgáltatók** **Szerkesztés** ikonjára.

    ![SAML-szolgáltatók szerkesztési ikonja](./media/bomgarremotesupport-tutorial/saml-providers.png)

1. Bontsa ki a **szolgáltatói beállítások** szakaszt.

1. Kattintson a **szolgáltatói metaadatok letöltése** elemre, vagy másolja az **entitás-azonosító** és az **ACS URL-** értékeket, és használja ezeket az értékeket a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    ![A szolgáltató metaadatainak letöltése](./media/bomgarremotesupport-tutorial/service-provider-metadata.png)


1. Az Identity Provider beállításai szakaszban kattintson az **Identity Provider metaadatainak feltöltése** elemre, és keresse meg a Azure Portal letöltött metaadat-XML-fájlt.

1.  A rendszer automatikusan feltölti az **entitás azonosítóját**, az **Sign-On szolgáltatás URL-címét** és a **kiszolgálói tanúsítványt** , és az **SSO URL-protokoll kötését** a **http post** értékre kell módosítani.

    ![A képernyőképen az Identity Provider beállításainak szakasza látható, ahol elvégezheti ezeket a műveleteket.](./media/bomgarremotesupport-tutorial/identity-provider.png)

1. Kattintson a **Save (Mentés**) gombra.

### <a name="create-beyondtrust-remote-support-test-user"></a>BeyondTrust távoli támogatási teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a BeyondTrust távoli támogatásban. A BeyondTrust-alapú távoli támogatás az igény szerinti felhasználói üzembe helyezést is támogatja, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a BeyondTrust távoli támogatásban, a hitelesítés után létrejön egy új.

Kövesse az alábbi eljárást, amely kötelező a BeyondTrust távoli támogatásának konfigurálásához.

A felhasználó-kiépítési beállításokat itt fogjuk konfigurálni. Az ebben a szakaszban használt értékeket a Azure Portal **felhasználói attribútumok & jogcímek** szakasza hivatkozik. Ezt úgy konfiguráltuk, hogy az a létrehozáskor már importált alapértelmezett értékek legyenek, azonban az érték szükség esetén testreszabható.

![Képernyőfelvétel: a felhasználói kiépítési beállítások, amelyekkel konfigurálhatja a felhasználói értékeket.](./media/bomgarremotesupport-tutorial/user-attribute.png)

> [!NOTE]
> A csoportok és az e-mail-attribútum nem szükséges ehhez a megvalósításhoz. Ha Azure AD-csoportokat használ, és hozzárendeli őket a BeyondTrust távoli támogatási csoportházirendekhez az engedélyekhez, a csoport objektum-AZONOSÍTÓját a Azure Portal és az "elérhető csoportok" szakaszban elhelyezett tulajdonságok segítségével kell hivatkozni. A művelet befejezését követően az objektum-azonosító/AD-csoport elérhető lesz a Csoportházirendhez való hozzárendeléshez az engedélyek számára.

![Képernyőfelvétel: az I T szakasz a tagsági típussal, a forrással, a típussal és a D objektummal.](./media/bomgarremotesupport-tutorial/config-user-2.png)

![Képernyőfelvétel: a csoportházirend alapszintű beállítások lapja.](./media/bomgarremotesupport-tutorial/group-policy.png)

> [!NOTE]
> Másik lehetőségként beállíthatja a egy SAML2 biztonsági szolgáltató alapértelmezett csoportházirendjét is. Ennek a beállításnak a megadásával a a csoportházirendben megadott engedélyek alapján az SAML-n keresztül hitelesítő összes felhasználót hozzárendeli. Az általános tagok házirend a BeyondTrust-alapú távoli támogatás/privilegizált távoli hozzáférés korlátozott engedélyekkel való használatának része, amely a hitelesítés tesztelésére és a felhasználók megfelelő szabályzatokhoz való hozzárendelésére használható. A felhasználók nem töltik be a egy SAML2-felhasználók listájára a belépési pont > felhasználóval & a biztonságot az első sikeres hitelesítési kísérletig. A csoportházirendekkel kapcsolatos további információk a következő hivatkozáson találhatók: `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a BeyondTrust távoli támogatási bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a BeyondTrust távoli támogatás bejelentkezési URL-címére, és indítsa el a bejelentkezési folyamatot innen.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások BeyondTrust távoli támogatás csempére kattint, a rendszer átirányítja a BeyondTrust távoli támogatás bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A BeyondTrust távoli támogatásának konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós idejű védelmet biztosít a szervezet bizalmas adatai kiszűrése és beszivárgásának. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáféréstől. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)