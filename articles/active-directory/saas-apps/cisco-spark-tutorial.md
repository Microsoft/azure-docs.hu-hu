---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Cisco WebEx | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Cisco WebEx között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: 23c2b55fd56677a15f444e1b568517bdf1c32c99
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200801"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Cisco WebEx

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Cisco WebEx Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Cisco WebEx-t, a következőket teheti:

* A Cisco WebEx hozzáférését biztosító Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Cisco WebEx az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Cisco WebEx egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Cisco WebEx támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.
* A Cisco WebEx támogatja a [**felhasználók automatikus üzembe**](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)helyezését.

## <a name="adding-cisco-webex-from-the-gallery"></a>Cisco-WebEx hozzáadása a katalógusból

A Cisco WebEx Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Cisco WebEx a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Cisco WebEx** kifejezést a keresőmezőbe.
1. Válassza ki a **Cisco WebEx** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex"></a>Az Azure AD SSO konfigurálása és tesztelése a Cisco WebEx

Konfigurálja és tesztelje az Azure AD SSO-t a Cisco WebEx egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Cisco WebEx.

Az Azure AD SSO és a Cisco WebEx konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. **[Konfigurálja a Cisco WebEx](#configure-cisco-webex)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. **[Hozzon létre Cisco WebEx-tesztelési felhasználót](#create-cisco-webex-test-user)** , hogy a "B. Simon" partnere legyen a Cisco WebEx, amely a felhasználó Azure ad-képviseletéhez van társítva.
3. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Cisco WebEx** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban töltse fel a letöltött **szolgáltatói metaadat** -fájlt, és konfigurálja az alkalmazást az alábbi lépések végrehajtásával:

    >[!Note]
    >A szolgáltatói metaadatokat a **Cisco WebEx konfigurálása** szakaszban találja, amelyet az oktatóanyag későbbi részében talál. 

    a. Kattintson a **metaadat-fájl feltöltése** elemre.

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés** elemre.

    c. A szolgáltatói metaadatok feltöltésének sikeres befejezése után az **azonosító** és a **Válasz URL-** értékei az **alapszintű SAML-konfiguráció** szakaszban automatikusan feltöltve lesznek:

    A **bejelentkezési URL** szövegmezőbe illessze be a **Válasz URL-címét**, amely az SP metaadat-fájl feltöltésekor automatikus kitöltést kap.

1. A Cisco WebEx-alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Cisco WebEx alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
  
    | Name |  Forrás attribútum|
    | ---------------|--------- |
    | UID | User. userPrincipalName |

    > [!NOTE]
    >  A forrás attribútum értéke alapértelmezés szerint a userpricipalname van leképezve. Ezt a User. mail vagy a User. onpremiseuserprincipalname, illetve bármely más értékre módosíthatja a WebEx beállításban.


1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Cisco WebEx.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Cisco WebEx** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-cisco-webex"></a>A Cisco WebEx konfigurálása

1. Jelentkezzen be a Cisco WebEx rendszergazdai hitelesítő adataival.

1. Válassza a **szervezeti beállítások** lehetőséget, majd a **hitelesítés** szakaszban kattintson a **módosítás** lehetőségre.

    ![A képernyőképen a módosítás lehetőségre kattintva megjelenítheti a hitelesítési beállításokat.](./media/cisco-spark-tutorial/organization-settings.png)
  
1. Válassza **a harmadik féltől származó identitás-szolgáltató integrálása lehetőséget. (Speciális)** kattintson a **tovább** gombra.

    ![A képernyőképen a harmadik féltől származó identitás-szolgáltató integrálása látható.](./media/cisco-spark-tutorial/enterprise-settings.png)

1. Kattintson a **metaadatok letöltése** elemre a **szolgáltatói metaadat-fájl** letöltéséhez és a számítógépbe való mentéséhez kattintson a **tovább** gombra.

    ![Képernyőfelvétel: a szolgáltatói metaadatokat megjelenítő fájl.](./media/cisco-spark-tutorial/sp-metadata.png)

1. Kattintson a **fájl tallózása** lehetőségre az Azure ad metaadat-fájljának megkereséséhez és feltöltéséhez. Ezután jelölje be **a hitelesítésszolgáltató által aláírt tanúsítvány megkövetelése a metaadatokban (biztonságosabb)** jelölőnégyzetet, majd kattintson a **tovább** gombra.

    ![Képernyőfelvétel: az import I d P metaadatok lapja.](./media/cisco-spark-tutorial/idp-metadata.png)

1. Válassza az **egyszeri bejelentkezéses csatlakozás tesztelése** lehetőséget, és amikor megnyílik egy új böngésző lap, jelentkezzen be az Azure ad-be.

1. Térjen vissza a **Cisco Cloud Collaboration Management** böngésző lapjára. Ha a teszt sikeres volt, válassza ki **a teszt sikerességét. Engedélyezze az egyszeri Sign-On lehetőséget** , és kattintson a **tovább** gombra.

1. Kattintson a **Mentés** gombra.

> [!NOTE]
> Ha többet szeretne megtudni a Cisco WebEx konfigurálásáról, tekintse meg [ezt](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) a lapot.

### <a name="create-cisco-webex-test-user"></a>Cisco WebEx-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Cisco WebEx. Ez az alkalmazás támogatja a felhasználók automatikus kiépítését, ami lehetővé teszi az üzleti szabályok alapján történő automatikus üzembe helyezést és megszüntetést.  A Microsoft ha lehetséges, az automatikus kiépítés használatát javasolja. Lásd: az automatikus kiépítés engedélyezése a [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial).

Ha manuálisan kell létrehoznia egy felhasználót, hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Cisco WebEx rendszergazdai hitelesítő adataival.

2. Kattintson a **felhasználók** , majd a **felhasználók kezelése** lehetőségre.
   
    ![Képernyőfelvétel: a felhasználók lap, amelyen kezelheti a felhasználókat.](./media/cisco-spark-tutorial/user-1.png) 

3. A **felhasználók kezelése** ablakban válassza a **felhasználók manuális hozzáadása vagy módosítása** lehetőséget.

    ![Képernyőfelvétel: a felhasználók lap, amelyen kezelheti a felhasználókat, és kiválaszthatja a felhasználók manuális hozzáadását vagy módosítását.](./media/cisco-spark-tutorial/user-2.png)

4. Válassza **a nevek és e-mail-cím** elemet. Ezután töltse ki a szövegmezőt a következő módon:

    ![Képernyőfelvétel: a felhasználók manuális hozzáadására és módosítására szolgáló panel.](./media/cisco-spark-tutorial/user-3.png) 

    a. Az **Utónév** szövegmezőbe írja be a (z) " **B**" felhasználó utónevét.

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét (például **Simon**).

    c. Az **e-mail cím** szövegmezőbe írja be a hasonló felhasználó e-mail címét b.simon@contoso.com .

5. Kattintson a plusz jelre a B. Simon hozzáadásához. Ezután kattintson a **tovább** gombra.

6. A **szolgáltatások hozzáadása a felhasználók** számára ablakban kattintson a **felhasználók hozzáadása** elemre, majd a **Befejezés** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez a szolgáltatás átirányítja a Cisco WebEx bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Cisco WebEx bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Cisco WebEx csempére kattint, a rendszer átirányítja a Cisco WebEx bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.


## <a name="next-steps"></a>Következő lépések

A Cisco WebEx konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)