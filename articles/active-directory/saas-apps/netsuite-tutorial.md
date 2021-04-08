---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a NetSuite-nal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a NetSuite között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: d99a19efcef0cae518d8d21d3371adaf37d32ff7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625481"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Oktatóanyag: az Azure AD egyszeri bejelentkezés (SSO) integrálása a NetSuite-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a NetSuite-t a Azure Active Directory (Azure AD) szolgáltatásba. Ha integrálja a NetSuite-t az Azure AD-vel, a következőket teheti:

* A NetSuite-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a NetSuite-ba az Azure AD-fiókjával.
* A fiókokat egy központi helyen, a Azure Portal kezelheti.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A NetSuite egyszeri bejelentkezés (SSO) használatára képes előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. 

A NetSuite a következőket támogatja:

* IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezés.
* JIT (igény szerinti) felhasználó kiépítés.

> [!NOTE]
> Mivel az alkalmazás azonosítója egy rögzített karakterlánc-érték, csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-netsuite-from-the-gallery"></a>A NetSuite hozzáadása a katalógusból

A NetSuite Azure AD-ba való integrálásának konfigurálásához adja hozzá a NetSuite-t a katalógusból a felügyelt SaaS-alkalmazások listájához a következő módon:

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók.
1. A bal oldali ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **NetSuite** kifejezést a keresőmezőbe.
1. Az eredmények ablaktábláján válassza a **NetSuite** lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a NetSuite-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a NetSuite használatával egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a NetSuite-beli kapcsolódó felhasználó között.

Az Azure AD SSO és a NetSuite konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.
    * [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon felhasználóval.  
    * [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy engedélyezze a B. Simon felhasználó számára az Azure ad egyszeri bejelentkezés használatát.
1. [Konfigurálja a NETSUITE SSO](#configure-netsuite-sso) -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    * [Hozza létre a NetSuite-teszt felhasználót](#create-the-netsuite-test-user) , hogy rendelkezzen egy, a felhasználó Azure ad-képviseletéhez csatolt B. Simon felhasználóval.
1. Ellenőrizze az [SSO](#test-sso) -t a konfiguráció működésének ellenőrzéséhez.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ha engedélyezni szeretné az Azure AD SSO használatát a Azure Portalban, tegye a következőket:

1. A Azure Portal a **NetSuite** Application Integration oldalon keresse meg a **kezelés** szakaszt, majd válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** ablaktáblán válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** panelen válassza az **ALAPszintű SAML-konfiguráció** melletti **Szerkesztés** ("ceruza") ikont.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban a **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő formátumok egyikében:

    ```https
    https://<Instance ID>.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.NetSuite.com/saml2/acs
    https://<Instance ID>.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.sandbox.NetSuite.com/saml2/acs
    ```

    * Az **<`Instance ID`>** értéket a NetSuite konfigurációs szakaszban találja meg, amelyet később az oktatóanyagban, a NetSuite-konfiguráció alatt, a 8. lépésben ismertetünk. Itt megtalálja a pontos tartományt (például system.na0.netsuite.com ebben az esetben).

        ![Képernyőfelvétel: SAML-telepítési oldal, ahol a tartományt szerezheti be.](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > Az előző URL-címek értékei nem valósak. Frissítse őket a tényleges válasz URL-címével. Az érték beszerzéséhez forduljon a [NetSuite ügyfél-támogatási csapathoz](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml). A Azure Portal az **alapszintű SAML-konfiguráció** szakaszának formátumait is megtekintheti.

1. A NetSuite-alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a NetSuite alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name | Forrás attribútum |
    | ---------------| --------------- |
    | account  | `account id` |

    > [!NOTE]
    > A fiók attribútum értéke nem valódi. Ezt az értéket az oktatóanyag későbbi részében leírtak szerint frissítheti.

1. Az egyszeri bejelentkezés az SAML-vel lapon az SAML aláíró tanúsítvány szakaszban keresse meg az összevonási metaadatok XML-fájlját, és válassza a letöltés lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A saját szükséglettől függően másolja ki a megfelelő URL-címet vagy URL-címeket az **NetSuite beállítása** szakaszban.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.

1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

1. A **felhasználó** tulajdonságai ablaktáblán hajtsa végre az alábbi lépéseket:

   a. A név mezőbe írja be a **B. Simon** **nevet** .  
   b. A **Felhasználónév** mezőbe írja be a username@companydomain.extension (például B.Simon@contoso.com ) nevet.  
   c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.  
   d. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezheti a B. Simon felhasználó számára az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a NetSuite-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **NetSuite** elemet.
1. Az Áttekintés ablaktáblán keresse meg a **kezelés** szakaszt, majd válassza a **felhasználók és csoportok** hivatkozást.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** ablaktábla **felhasználók** legördülő listájában válassza ki a **B. Simon** elemet, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkör-értéket vár, tegye a következőket:

   a. A **szerepkör kiválasztása** ablaktáblában, a legördülő listából válassza ki a megfelelő szerepkört a felhasználó számára.  
   b. A képernyő alján kattintson a **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** panelen kattintson a **hozzárendelés** gombra.

## <a name="configure-netsuite-sso"></a>A NetSuite SSO konfigurálása

1. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a NetSuite vállalati webhelyre rendszergazdaként.

2. A felső navigációs sávon válassza a **beállítás** lehetőséget, majd válassza a **vállalat**  >  **engedélyezése funkciók** lehetőséget.

    ![A képernyőképen a vállalattól kiválasztott szolgáltatások engedélyezése látható.](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Az oldal közepén található eszköztáron válassza a **SuiteCloud** lehetőséget.

    ![A képernyőképen a kiválasztott SuiteCloud látható.](./media/NetSuite-tutorial/ns-suitecloud.png)

4. A **hitelesítés kezelése** területen jelölje be az **SAML egyszeri bejelentkezés** jelölőnégyzetet az SAML egyszeri bejelentkezés beállítás engedélyezéséhez a NetSuite-ban.

    ![Képernyőfelvétel: az SAML egyszeri bejelentkezés kiválasztásához használható hitelesítés kezelése.](./media/NetSuite-tutorial/ns-ticksaml.png)

5. A felső navigációs sávon válassza a **telepítés** lehetőséget.

    ![Képernyőfelvétel: a rendszer a NETSUITE navigációs sávon kiválasztott telepítőt jeleníti meg.](./media/NetSuite-tutorial/ns-setup.png)

6. A **beállítási feladatok** listában válassza az **integráció** lehetőséget.

    ![A képernyőképen a telepítési feladatokból kiválasztott integráció látható.](./media/NetSuite-tutorial/ns-integration.png)

7. A **hitelesítés kezelése** területen válassza az **SAML egyszeri bejelentkezés** lehetőséget.

    ![Képernyőfelvétel: a beállítási feladatok integrációs eleméből kiválasztott SAML egyszeri bejelentkezés.](./media/NetSuite-tutorial/ns-saml.png)

8. Az **SAML beállítása** ablaktábla **NetSuite konfigurálása** területén tegye a következőket:

    ![Képernyőfelvétel: SAML-beállítás, ahol megadhatja a leírt értékeket.](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Jelölje be az **elsődleges hitelesítési módszer** jelölőnégyzetet.

    b. Az **SAMLV2-szolgáltató metaadatainak** területén válassza a **identitásszolgáltató-metaadatok feltöltése** lehetőséget, majd a **Tallózás** gombra kattintva töltse fel a letöltött metaadat-fájlt a Azure Portal.

    c. Válassza a **Küldés** lehetőséget.

9. A NetSuite felső navigációs sávban válassza a **beállítás**, majd a **vállalati**  >  **vállalati adatok** elemet.

    ![A képernyőképen a vállalattól kiválasztott vállalati információk láthatók.](./media/NetSuite-tutorial/ns-com.png)

    ![Képernyőfelvétel: az a panel, amelyen megadhatja a leírt értékeket.](./media/NetSuite-tutorial/ns-account-id.png)

    b. A **Vállalati információk** ablaktáblán, a jobb oldali oszlopban másolja ki a **fiókazonosító** értékét.

    c. Illessze be a NetSuite-fiókból másolt **fiók azonosítóját** az Azure ad **attribútum érték** mezőjébe.

    ![Képernyőfelvétel – a fiókazonosító értékének hozzáadása](./media/netsuite-tutorial/attribute-value.png)

10. Ahhoz, hogy a felhasználók egyszeri bejelentkezést tudjanak végezni a NetSuite-ba, először hozzá kell rendelni a megfelelő engedélyeket a NetSuite-ban. Az engedélyek hozzárendeléséhez tegye a következőket:

    a. A felső navigációs sávon válassza a **telepítés** lehetőséget.

    ![Képernyőfelvétel: a rendszer a NETSUITE navigációs sávon kiválasztott telepítőt jeleníti meg.](./media/NetSuite-tutorial/ns-setup.png)

    b. A bal oldali ablaktáblán válassza a **felhasználók/szerepkörök**, majd a **Szerepkörök kezelése** lehetőséget.

    ![Képernyőfelvétel: a Szerepkörök kezelése panel, ahol új szerepkört választhat.](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Válassza az **Új szerepkör** lehetőséget.

    d. Adja meg az új szerepkör **nevét** .

    ![Képernyőfelvétel: a telepítéskezelő, ahol megadhatja a szerepkör nevét.](./media/NetSuite-tutorial/ns-new-role.png)

    e. Kattintson a **Mentés** gombra.

    f. A felső navigációs sávon válassza az **engedélyek** lehetőséget. Ezután válassza a **telepítés** lehetőséget.

    ![Képernyőfelvétel: a telepítés lap, amelyen megadhatja a leírt értékeket.](./media/NetSuite-tutorial/ns-sso.png)

    : Válassza az **SAML egyszeri bejelentkezés** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.

    h. Kattintson a **Mentés** gombra.

    i. A felső navigációs sávon válassza a **beállítás**, majd a **telepítéskezelő** lehetőséget.

    ![Képernyőfelvétel: a rendszer a NETSUITE navigációs sávon kiválasztott telepítőt jeleníti meg.](./media/NetSuite-tutorial/ns-setup.png)

    j. A bal oldali ablaktáblán válassza a **felhasználók/szerepkörök** lehetőséget, majd válassza a **felhasználók kezelése** lehetőséget.

    ![Képernyőfelvétel: a felhasználók kezelése panel, ahol kiválaszthatja a Suite demó csapatát.](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Válasszon ki egy teszt felhasználót, válassza a **Szerkesztés** lehetőséget, majd válassza a **hozzáférés** lapot.

    ![Képernyőfelvétel: a felhasználók kezelése panel, ahol a szerkesztés lehetőséget választhatja.](./media/NetSuite-tutorial/ns-edit-user.png)

    l. A **szerepkörök** ablaktáblán rendelje hozzá a létrehozott megfelelő szerepkört.

    ![A képernyőképen az alkalmazottak közül kiválasztott rendszergazda látható.](./media/NetSuite-tutorial/ns-add-role.png)

    m. Kattintson a **Mentés** gombra.

### <a name="create-the-netsuite-test-user"></a>A NetSuite test felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a NetSuite-ban. A NetSuite az igény szerinti felhasználói üzembe helyezést is támogatja, ami alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs művelet. Ha egy felhasználó még nem létezik a NetSuite-ban, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

- Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a NetSuite-ra, amelyhez be szeretné állítani az egyszeri bejelentkezést

- Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások NetSuite csempére kattint, automatikusan be kell jelentkeznie arra a NetSuite-ra, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A NetSuite konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáféréstől. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)