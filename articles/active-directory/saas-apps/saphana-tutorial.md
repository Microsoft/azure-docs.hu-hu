---
title: 'Oktatóanyag: Azure Active Directory integráció a SAP HANAtal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SAP HANA között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 1a1e155974b66dce9a036a20cdebe19ded81fed5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727081"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Oktatóanyag: Azure Active Directory integráció a SAP HANA

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a SAP HANAt Azure Active Directory (Azure AD) használatával. A SAP HANA az Azure AD-vel való integrálásakor a következőket teheti:

* A SAP HANAhoz hozzáféréssel rendelkező Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az Azure AD-fiókjával SAP HANAba.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció SAP HANAval való konfigurálásához a következő elemek szükségesek:

- Azure AD-előfizetés
- Az egyszeri bejelentkezést (SSO) engedélyező SAP HANA előfizetés
- Olyan HANA-példány, amely nyilvános IaaS, helyszíni, Azure-beli virtuális gépen vagy nagy méretű SAP-példányokon fut az Azure-ban
- A XSA felügyelet webes felülete, valamint a Hana-példányra telepített HANA Studio

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem ajánlott SAP HANA éles környezetét használni. Először tesztelje az integrációt az alkalmazás fejlesztési vagy átmeneti környezetében, majd használja az éles környezetet.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi javaslatokat:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* SAP HANA egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* SAP HANA támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* SAP HANA támogatja **az** igény szerinti felhasználói üzembe helyezést

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.


## <a name="adding-sap-hana-from-the-gallery"></a>SAP HANA hozzáadása a gyűjteményből

SAP HANA az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia SAP HANA a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **SAP HANA** kifejezést a keresőmezőbe.
1. Válassza ki **SAP HANA** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>Azure AD SSO konfigurálása és tesztelése SAP HANA

Az Azure AD SSO konfigurálása és tesztelése SAP HANA egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között SAP HANAban.

Az Azure AD SSO konfigurálásához és teszteléséhez SAP HANA használatával hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. **[SAP HANA SSO konfigurálása](#configure-sap-hana-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre SAP HANA test User](#create-sap-hana-test-user)** -t, hogy a Britta Simon partnere legyen a SAP HANA, amely a felhasználó Azure ad-képviseletéhez van csatolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **SAP HANA** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. Frissítse az értéket a tényleges válasz URL-címével. Az értékek lekéréséhez vegye fel a kapcsolatot [SAP HANA ügyfél-támogatási csapattal](https://cloudplatform.sap.com/contact.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. SAP HANA alkalmazás megadott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon, a **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![A "szerkesztés" ikonnal jelölt "felhasználói attribútumok" szakaszt megjelenítő képernyőkép.](common/edit-attribute.png)

6. A felhasználói **attribútumok** **& a jogcímek** párbeszédpanelen hajtsa végre a következő lépéseket:
 
    a. Kattintson a **Szerkesztés ikonra** a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![Képernyőfelvétel: a "szerkesztés" ikon kiválasztásával megjelenítheti a "felhasználói attribútumok & jogcímek" párbeszédpanelt.](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Az **átalakítás** listából válassza a **ExtractMailPrefix ()** elemet.

    c. Az **1. paraméter** listából válassza a **User. mail** elemet.

    d. Kattintson a **Mentés** gombra.

7. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát, ha hozzáférést biztosít a SAP HANAhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **SAP HANA** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sap-hana-sso"></a>SAP HANA SSO konfigurálása

1. Ha az egyszeri bejelentkezést az SAP HANA oldalon szeretné konfigurálni, jelentkezzen be a **HANA XSA Webkonzolra**  a megfelelő HTTPS-végponton.

    > [!NOTE]
    > Az alapértelmezett konfigurációban az URL-cím átirányítja a kérést egy bejelentkezési képernyőre, amelyhez hitelesített SAP HANA adatbázis-felhasználó hitelesítő adatai szükségesek. A bejelentkező felhasználónak rendelkeznie kell az SAML felügyeleti feladatok végrehajtásához szükséges engedélyekkel.

2. A XSA webes felületén nyissa meg az **SAML-identitás szolgáltatóját**. Innen válassza a **+** képernyő alján található gombot az **Identity Provider információinak hozzáadása** panel megjelenítéséhez. Ezután hajtsa végre a következő lépéseket:

    ![Identitás-szolgáltató hozzáadása](./media/saphana-tutorial/sap1.png)

    a. Az **Identity Provider információinak hozzáadása** panelen illessze be a metaadatok XML-fájljának tartalmát (amelyet a Azure Portalból töltött le) a **metaadatok** mezőbe.

    ![Képernyőkép, amely az "Identity Provider-adatok hozzáadása" panelt jeleníti meg a "metaadatok" és a "név" mezők kiemelésével.](./media/saphana-tutorial/sap2.png)

    b. Ha az XML-dokumentum tartalma érvényes, az elemzési folyamat kibontja az **általános** adatképernyő területén a **tulajdonos, az entitás-azonosító és a kiállító** mezőkhöz szükséges adatokat. Emellett kigyűjti a **célként** megadott képernyő URL mezőihez szükséges adatokat, például az **alap URL-címet és a SingleSignon URL-címét (*)** .

    ![Identitás-szolgáltató beállításainak megadása](./media/saphana-tutorial/sap3.png)

    c. Az **általános** adatképernyő terület **név** mezőjébe írja be az új SAML SSO-azonosító nevét.

    > [!NOTE]
    > Az SAML-IDENTITÁSSZOLGÁLTATÓ neve kötelező, és egyedinek kell lennie. Megjelenik a rendelkezésre álló SAML-IDP listájában, amely akkor jelenik meg, ha a SAP HANA XS-alkalmazások használatára vonatkozó hitelesítési módszerként kiválasztja az SAML-t. Ezt például az XS-összetevő felügyeleti eszközének **hitelesítési** képernyő területén teheti meg.

3. A **Save (Mentés** ) gombra kattintva mentheti a SAML-identitás szolgáltatójának adatait, és hozzáadhatja az új SAML-identitásszolgáltató az ismert SAML-IDP listájához.

    ![Mentés gomb](./media/saphana-tutorial/sap4.png)

4. A HANA Studióban, a **konfiguráció** lap rendszertulajdonságai között, az **SAML** alapján szűrheti a beállításokat. Ezután módosítsa a **assertion_timeout** **10 másodperc** és **120 másodperc** közötti értékre.

    ![assertion_timeout beállítás](./media/saphana-tutorial/sap7.png)


### <a name="create-sap-hana-test-user"></a>SAP HANA tesztelési felhasználó létrehozása

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezzenek a SAP HANAba, be kell építenie azokat a SAP HANAban.
SAP HANA támogatja az igény szerinti **üzembe** helyezést, amely alapértelmezés szerint engedélyezve van.

Ha manuálisan kell létrehoznia egy felhasználót, hajtsa végre a következő lépéseket:

>[!NOTE]
>Módosíthatja a felhasználó által használt külső hitelesítést. Külső rendszerekkel, például Kerberos-hitelesítéssel is hitelesíthetők. A külső identitásokkal kapcsolatos részletes információkért forduljon a [tartományi rendszergazdához](https://cloudplatform.sap.com/contact.html).

1. Nyissa meg a [SAP HANA studiót](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) rendszergazdaként, majd engedélyezze a DB-User SAML SSO-hoz.

    ![Felhasználó létrehozása](./media/saphana-tutorial/sap5.png)

2. Jelölje be a láthatatlan jelölőnégyzetet az **SAML** bal oldalán, majd válassza a **Konfigurálás** hivatkozást.

3. Válassza a **Hozzáadás** lehetőséget az SAML-identitásszolgáltató hozzáadásához.  Válassza ki a megfelelő SAML-IDENTITÁSSZOLGÁLTATÓ, majd kattintson **az OK gombra**.

4. Adja hozzá a **külső identitást** (ebben az esetben BrittaSimon), **vagy válasszon egyet**. Ez után válassza az **OK** gombot.

   > [!Note]
   > Ha a **bármely** jelölőnégyzet nincs bejelölve, akkor a HANA-beli felhasználónévnek pontosan egyeznie kell a felhasználó nevével az UPN-ben a tartomány utótagja előtt. (Például BrittaSimon@contoso.com BrittaSimon válik a HANA-ban.)

5. Tesztelési célból rendeljen hozzá minden **XS** -szerepkört a felhasználóhoz.

    ![Szerepkörök kiosztása](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Olyan engedélyeket kell megadni, amelyek csak a használati esetekben megfelelőek.

6. Mentse a felhasználót.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a SAP HANAra, amelyhez be szeretné állítani az egyszeri bejelentkezést

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások SAP HANA csempére kattint, automatikusan be kell jelentkeznie arra a SAP HANAra, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A SAP HANA konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).