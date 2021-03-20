---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a EBSCO | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és EBSCO között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.openlocfilehash: 2f7f017d773e7708b2206029769df7977b12e6f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454337"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a EBSCO

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a EBSCO a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az EBSCO-t az Azure AD-vel, a következőket teheti:

* A EBSCO-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a EBSCO az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* EBSCO egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A EBSCO támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO
* A EBSCO **csak időben támogatja a** felhasználók kiépítési folyamatát

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-ebsco-from-the-gallery"></a>EBSCO hozzáadása a gyűjteményből

A EBSCO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a EBSCO a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **EBSCO** kifejezést a keresőmezőbe.
1. Válassza ki a **EBSCO** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a EBSCO

Konfigurálja és tesztelje az Azure AD SSO-t a EBSCO a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a EBSCO-ben.

Az Azure AD SSO és a EBSCO konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[EBSCO SSO konfigurálása](#configure-ebsco-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre EBSCO-teszt felhasználót](#create-ebsco-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-EBSCO rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **EBSCO** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    Az **azonosító** szövegmezőbe írja be az URL-címet:  `pingsso.ebscohost.com`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > A bejelentkezési URL-cím értéke nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a EBSCO ügyfélszolgálati [csapatához](mailto:support@ebsco.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

    o   **egyedi elemek:**  

    o   **CustId** = egyedi EBSCO-ügyfél azonosítójának megadása 

    o   **profil** = az ügyfelek testre szabhatók a hivatkozás, hogy egy adott profilhoz irányítsák a felhasználókat (attól függően, hogy mit vásárolnak a EBSCO). Megadhatnak egy adott profil-azonosítót. A fő azonosítók az EDS (EBSCO Discovery Service) és a ehost (EBSOCOhost-adatbázisok). Az itt megjelenő utasításokat [itt](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile)találja.

1. A EBSCO alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

    > [!Note]
    > A **Name** attribútum kötelező, és a EBSCO alkalmazásban a **Name Identifier érték** van leképezve. Ez alapértelmezés szerint hozzá van adva, így nem kell manuálisan hozzáadnia.

1. A fentiek mellett a EBSCO alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name | Forrás attribútum|
    | ---------------| --------------- |
    | FirstName   | User. givenName |
    | LastName   | felhasználó. vezetéknév |
    | E-mail   | User. mail |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **EBSCO beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a EBSCO.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **EBSCO** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-ebsco-sso"></a>EBSCO SSO konfigurálása

Ha az egyszeri bejelentkezést szeretné konfigurálni a **EBSCO** oldalon, el kell küldenie a letöltött **összevonási metaadatokat tartalmazó XML-fájlt** és a megfelelő másolt url-címeket a Azure Portal a [EBSCO támogatási csapatának](mailto:support@ebsco.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-ebsco-test-user"></a>EBSCO-tesztelési felhasználó létrehozása

A EBSCO esetében a felhasználók kiosztása automatikus.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

Az Azure AD továbbítja a szükséges EBSCO alkalmazást. A EBSCO felhasználó-kiépítés lehet automatikus, vagy egy egyszeri űrlapra van szükség. Attól függ, hogy az ügyfél rendelkezik-e sok olyan meglévő EBSCOhost-fiókkal, amelyeken személyes beállítások lettek mentve. Ugyanezt a [EBSCO támogatási csapatával](mailto:support@ebsco.com) is megvitathatja a megvalósítás során. Mindkét esetben az ügyfélnek nem kell EBSCOhost-fiókokat létrehoznia a tesztelés előtt.

   > [!Note]
   > Automatizálhatja a EBSCOhost-felhasználók üzembe helyezését vagy személyre szabását. Az igény szerinti felhasználói üzembe helyezéssel kapcsolatban forduljon a [EBSCO ügyfélszolgálatához](mailto:support@ebsco.com) .

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

1. Ha a hozzáférési panelen a EBSCO csempére kattint, a rendszer automatikusan bejelentkezett a EBSCO alkalmazásba.
További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

1. Miután bejelentkezett az alkalmazásba, kattintson a jobb felső sarokban található **Bejelentkezés** gombra.

    ![Az EBSCO-bejelentkezés az alkalmazások listájában](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. A rendszer egyszeri kérést kap az intézményi/SAML-bejelentkezés összepárosításához, amely egy **meglévő MyEBSCOhost-fiókjának az intézmény-fiókjához való csatolását** veszi igénybe, vagy **létrehoz egy új MyEBSCOhost-fiókot, és összekapcsolja azt az intézmény fiókjával**. A fiók a EBSCOhost alkalmazás személyre szabására szolgál. Válassza az **új fiók létrehozása** lehetőséget, és látni fogja, hogy a személyre szabás űrlapja előre be van töltve az SAML-válaszból származó értékekkel, ahogy az alábbi képernyőképen is látható. A kijelölés mentéséhez kattintson a **"Folytatás"** gombra.
    
     ![A EBSCO felhasználó az alkalmazások listájában](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. A fenti beállítás befejezése után törölje a cookie-k/gyorsítótár és a bejelentkezés újbóli beírását. Nem kell manuálisan bejelentkeznie, és a rendszer megjegyezi a személyre szabási beállításokat.

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A EBSCO kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)