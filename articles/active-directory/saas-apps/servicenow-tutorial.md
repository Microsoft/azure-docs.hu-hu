---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a ServiceNow | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ServiceNow között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: c90234249f3cf7eb6ed4793110d61e1f8190ed60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99092633"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a ServiceNow

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ServiceNow a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az ServiceNow-t az Azure AD-vel, a következőket teheti:

* A ServiceNow-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a ServiceNow az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Jao6]

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* ServiceNow egyszeri bejelentkezést (SSO) engedélyező előfizetés.
* A ServiceNow esetében a ServiceNow egy példánya vagy bérlője a Calgary, Kingston, London, Madrid, New York, Orlando és Párizs vagy újabb verziókat támogatja.
* A ServiceNow Express esetében a ServiceNow Express, a Helsinki vagy újabb verzió példánya.
* Az ServiceNow-bérlőnek engedélyezve kell lennie a [több szolgáltató egyszeri bejelentkezés beépülő moduljának](https://old.wiki/index.php/Multiple_Provider_Single_Sign-On#gsc.tab=0) .
* Az automatikus konfiguráláshoz engedélyezze a többszörös szolgáltató beépülő modult a ServiceNow.
* A klasszikus ServiceNow (Mobile) alkalmazás telepítéséhez lépjen a megfelelő tárolóba, és keresse meg a klasszikus ServiceNow alkalmazást. Ezután töltse le.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. 

* A ServiceNow támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

* A ServiceNow támogatja az [automatikus felhasználó-kiépítés](servicenow-provisioning-tutorial.md)használatát.

* A ServiceNow klasszikus (Mobile) alkalmazást az Azure AD-vel konfigurálhatja az egyszeri bejelentkezés engedélyezéséhez. Android és iOS rendszerű felhasználókat is támogat. Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

## <a name="add-servicenow-from-the-gallery"></a>ServiceNow hozzáadása a gyűjteményből

A ServiceNow Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ServiceNow a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, és válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban adja meg a **ServiceNow** kifejezést a keresőmezőbe.
1. Válassza ki a **ServiceNow** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-servicenow"></a>Azure AD SSO konfigurálása és tesztelése a ServiceNow-hez

Konfigurálja és tesztelje az Azure AD SSO-t a ServiceNow egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a ServiceNow-ben.

Az Azure AD SSO és a ServiceNow konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.
    1. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
    1. [Konfigurálja az Azure ad SSO-t a ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express) -be, hogy a felhasználók használhassák ezt a funkciót.
2. [Konfigurálja a ServiceNow](#configure-servicenow) az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. [Hozzon létre egy ServiceNow-tesztelési felhasználót](#create-servicenow-test-user) , hogy az a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon (ServiceNow) párja legyen.
    1. [Konfigurálja a ServiceNow Express SSO](#configure-servicenow-express-sso) -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.  
3. Ellenőrizze az [SSO](#test-sso) -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.
4. [Tesztelje az SSO-t a klasszikus ServiceNow (Mobile)](#test-sso-for-servicenow-classic-mobile) annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **ServiceNow** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt. Válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az **alapszintű SAML-konfigurációhoz** tartozó toll ikont a beállítások szerkesztéséhez.

   ![Képernyőfelvétel az egyszeri Sign-On az SAML-oldallal való beállításáról, a toll ikon kiemelve](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** mezőben adjon meg egy URL-címet, amely a következő mintát használja: `https://<instancename>.service-now.com/navpage.do`

    b. Az **azonosító (entitás azonosítója)** mezőben adjon meg egy URL-címet, amely a következő mintát használja: `https://<instance-name>.service-now.com`

    c. A **Válasz URL-cím** mezőben adja meg a következő URL-minták egyikét:

    | Válasz URL-cím|
    |----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` | 

    d. A **kijelentkezési URL-cím** mezőben adjon meg egy URL-címet, amely a következő mintát használja: `https://<instancename>.service-now.com/navpage.do`

    > [!NOTE]
    > Ha a "/" értéket adja hozzá az azonosító értékhez, távolítsa el manuálisan.

    > [!NOTE]
    > Ezek az értékek nem valósak. Ezeket az értékeket a tényleges bejelentkezési URL-címmel, a válasz URL-címével, a kijelentkezési URL-címmel és az azonosítóval kell frissíteni, amelyet az oktatóanyag későbbi részében ismertetünk. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** című szakaszt. 

   ![Képernyőkép az SAML aláíró tanúsítványról, a letöltés kiemelve](common/certificatebase64.png)

   a. A Másolás gombra kattintva másolja a vágólapra az **alkalmazás-összevonási metaadatok URL-címét**, és illessze be a Jegyzettömbbe. Ezt az URL-címet az oktatóanyag későbbi részében fogja használni.

    b. Válassza a **Letöltés** elemet a tanúsítvány letöltéséhez **(Base64)**, majd mentse a tanúsítványt a számítógépre.

1. A **ServiceNow beállítása** szakaszban másolja a megfelelő URL-címeket a követelmény alapján.

   ![Képernyőkép a ServiceNow beállítása szakaszról, a Kiemelt URL-címekkel](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **név** mezőbe írja be a következőt: `B.Simon` .  
   1. A **Felhasználónév** mezőbe írja be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Válassza a **jelszó megjelenítése** lehetőséget, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a ServiceNow.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **ServiceNow** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **Felhasználó hozzáadása** elemet. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza ki az **B. Simon** elemet a felhasználók listából, majd válassza a **kiválasztás** lehetőséget.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Az Azure AD SSO konfigurálása a ServiceNow expresshez

1. A [Azure Portal](https://portal.azure.com/) **ServiceNow** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Képernyőfelvétel a ServiceNow alkalmazás-integrációs oldaláról, egyszeri bejelentkezéssel kiemelve](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Képernyőkép az egyszeri bejelentkezési módszer kiválasztásáról, az SAML kiemelve](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML** használatával lapon válassza a toll ikont az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Az egyszeri bejelentkezés SAML-oldallal való beállításának képernyőképe, a toll ikon kiemelve](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** mezőben adjon meg egy URL-címet, amely a következő mintát használja: `https://<instancename>.service-now.com/navpage.do`

    b. Az **azonosító (entitás azonosítója)** mezőben adjon meg egy URL-címet, amely a következő mintát használja: `https://<instance-name>.service-now.com`

    c. A **Válasz URL-cím** mezőben adja meg a következő URL-cím egyikét:

    | Válasz URL-cím |
    |-----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` |

    d. A **kijelentkezési URL-cím** mezőben adjon meg egy URL-címet, amely a következő mintát használja: `https://<instancename>.service-now.com/navpage.do`
    
    > [!NOTE]
    > Ha a "/" értéket adja hozzá az azonosító értékhez, távolítsa el manuálisan.

    > [!NOTE]
    > Ezek az értékek nem valósak. Ezeket az értékeket a tényleges bejelentkezési URL-címmel, a válasz URL-címével, a kijelentkezési URL-címmel és az azonosítóval kell frissíteni, amelyet az oktatóanyag későbbi részében ismertetünk. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeri bejelentkezés az SAML-vel lapon való beállításához** az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, az igény szerint. Mentse a számítógépére.

    ![Képernyőfelvétel az SAML-aláíró tanúsítványról szakasz, a letöltés kiemelve](common/certificatebase64.png)

6. Az Azure AD automatikusan konfigurálhatja az SAML-alapú hitelesítés ServiceNow. A szolgáltatás engedélyezéséhez lépjen a **ServiceNow beállítása** szakaszra, és válassza a **lépésenkénti útmutatások megjelenítése** lehetőséget a **Bejelentkezés konfigurálása** ablak megnyitásához.

    ![Képernyőkép a ServiceNow beállításáról, részletes utasításokkal](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

7. A **bejelentkezési űrlap konfigurálása** lapon adja meg a ServiceNow-példány nevét, a rendszergazdai felhasználónevet és a rendszergazdai jelszót. Válassza a **Konfigurálás most** lehetőséget. A megadott rendszergazdai felhasználónévnek rendelkeznie kell a ServiceNow-hez hozzárendelt **security_admin** szerepkörrel ahhoz, hogy működjön. Ha nem szeretné manuálisan konfigurálni az Azure AD-t SAML-ServiceNow, válassza az **egyszeri bejelentkezés manuális konfigurálása** lehetőséget. Másolja ki a **kijelentkezési URL-címet, az Azure ad-azonosítót és a bejelentkezési URL-címet** a rövid útmutató szakaszban.

    ![Képernyőkép a bejelentkezési űrlap konfigurálásáról, a konfigurálás most kiemelve](./media/servicenow-tutorial/configure.png "Alkalmazás URL-címének konfigurálása")

## <a name="configure-servicenow"></a>ServiceNow konfigurálása

1. Jelentkezzen be a ServiceNow alkalmazásba rendszergazdaként.

1. Aktiválja az **integrációt – több szolgáltató egyszeri bejelentkezéses telepítő** beépülő modulját a következő lépésekkel:

    a. A bal oldali ablaktáblán keresse meg a **rendszerdefiníció** szakaszt a keresőmezőbe, majd válassza a **plugins** lehetőséget.

    ![Képernyőkép a rendszerdefiníciós szakaszról, a rendszerdefinícióval és a beépülő modulok kiemelésével](./media/servicenow-tutorial/tutorial-servicenow-03.png "Beépülő modul aktiválása")

    b. Integráció keresése **– több szolgáltató egyszeri bejelentkezési telepítője**.

     ![Képernyőkép a System plugins oldalról, integrációval – több szolgáltató egyetlen Sign-On telepítőjének kiemelve](./media/servicenow-tutorial/tutorial-servicenow-04.png "Beépülő modul aktiválása")

    c. Válassza ki a beépülő modult. Kattintson a jobb gombbal, majd válassza az **aktiválás/frissítés** lehetőséget.

     ![Képernyőkép – a jobb gombbal a menüre, az aktiválás/frissítés kiemelve lehetőséggel](./media/servicenow-tutorial/tutorial-activate.png "Beépülő modul aktiválása")

    d. Válassza az **aktiválás** lehetőséget.

     ![A beépülő modul aktiválása párbeszédpanel képernyőképe a Kiemelt aktiválással](./media/servicenow-tutorial/tutorial-activate-1.png "Beépülő modul aktiválása")

1. A bal oldali ablaktáblán keresse meg a **több szolgáltatótól származó egyszeri bejelentkezés** szakaszt a keresősáv alatt, majd válassza a **Tulajdonságok** lehetőséget.

    ![Képernyőfelvétel a többszolgáltatós egyszeri bejelentkezésről szakasz, a több szolgáltatótól származó egyszeri bejelentkezés és a tulajdonságok kiemelésével](./media/servicenow-tutorial/tutorial-servicenow-06.png "Alkalmazás URL-címének konfigurálása")

1. A **több szolgáltató egyszeri bejelentkezésének tulajdonságai** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Képernyőkép a több szolgáltató egyszeri bejelentkezésének tulajdonságai párbeszédpanelről](./media/servicenow-tutorial/ic7694981.png "Alkalmazás URL-címének konfigurálása")

    * **Több szolgáltató egyszeri bejelentkezésének engedélyezéséhez** válassza az **Igen** lehetőséget.
  
    * Ha engedélyezni kívánja a **felhasználók automatikus importálását az összes identitás-szolgáltatóból a felhasználói táblába**, válassza az **Igen** lehetőséget.

    * A **több szolgáltató egyszeri bejelentkezéses integrációjának hibakeresési naplózásának engedélyezéséhez** válassza az **Igen** lehetőséget.

    * A **felhasználói tábla azon mezőjénél, amely...**, írja be az **e-mail címet**.
  
    * Kattintson a **Mentés** gombra.

1. A ServiceNow automatikusan vagy manuálisan is konfigurálhatja. A ServiceNow automatikus konfigurálásához kövesse az alábbi lépéseket:

    1. Térjen vissza a Azure Portal **ServiceNow** egyszeri bejelentkezési lapjára.

    1. A ServiceNow esetében egy kattintással konfigurálható a szolgáltatás. A szolgáltatás engedélyezéséhez lépjen a ServiceNow- **konfiguráció** szakaszra, és válassza a **ServiceNow konfigurálása** elemet a **Bejelentkezés konfigurálása** ablak megnyitásához.

        ![Képernyőkép a ServiceNow beállításáról – részletes utasítások](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

    1. A **bejelentkezési űrlap konfigurálása** lapon adja meg a ServiceNow-példány nevét, a rendszergazdai felhasználónevet és a rendszergazdai jelszót. Válassza a **Konfigurálás most** lehetőséget. A megadott rendszergazdai felhasználónévnek a ServiceNow-ben hozzárendelt **biztonsági rendszergazdai** szerepkörrel kell rendelkeznie ahhoz, hogy működjön. Ha nem szeretné manuálisan konfigurálni az Azure AD-t SAML-ServiceNow, válassza az **egyszeri bejelentkezés manuális konfigurálása** lehetőséget. Másolja ki a kijelentkezési **URL-címet, az SAML-entitás azonosítóját és az SAML egyszeri bejelentkezés szolgáltatás URL-címét** a rövid útmutató szakaszban.

        ![Képernyőkép a bejelentkezési űrlap konfigurálásáról, a konfigurálás most kiemelve](./media/servicenow-tutorial/configure.png "Alkalmazás URL-címének konfigurálása")

    1. Jelentkezzen be a ServiceNow alkalmazásba rendszergazdaként.

       * Az automatikus konfigurációban az összes szükséges beállítás a **ServiceNow** oldalon van konfigurálva, de az **X. 509 tanúsítvány** alapértelmezés szerint nincs engedélyezve, és az **egyetlen Sign-On parancsfájl** értékét **MultiSSOv2_SAML2_customként** adja meg. A ServiceNow-ben manuálisan kell leképeznie az identitás-szolgáltatónak. Kövesse az alábbi lépéseket:

         1. A bal oldali ablaktáblán keresse meg a **Többszolgáltatós egyszeri bejelentkezés** szakaszt a keresőmezőbe, és válassza az **identitás-szolgáltatók** elemet.

            ![Képernyőfelvétel a többszolgáltatós egyszeri bejelentkezésről szakasz](./media/servicenow-tutorial/tutorial-servicenow-07.png "Egyszeri bejelentkezés konfigurálása")

         1. Válassza ki az automatikusan létrehozott identitás-szolgáltatót.

            ![Képernyőkép az identitás-szolgáltatókról, az automatikusan generált identitás-szolgáltató kiemelve](./media/servicenow-tutorial/tutorial-servicenow-08.png "Egyszeri bejelentkezés konfigurálása")

         1.  Az **identitás-szolgáltató** szakaszban hajtsa végre a következő lépéseket:

             ![Képernyőkép az Identity Provider szakaszról](./media/servicenow-tutorial/automatic-config.png "Egyszeri bejelentkezés konfigurálása")

               a. A **név** mezőben adja meg a konfiguráció nevét (például **Microsoft Azure összevont egyszeri bejelentkezés**).

               b. Másolja a **ServiceNow homepage** értéket, és illessze be a **bejelentkezési URL-cím** mezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszának ServiceNow.

                > [!NOTE]
                > A ServiceNow-példány kezdőlapja a **ServiceNow-bérlő URL-címének** és **/navpage.do** összefűzése (például: `https://fabrikam.service-now.com/navpage.do` ).

              c. Másolja ki az **entitás azonosítója/kiállító** értékét, és illessze be az azonosítót a Azure Portal **ServiceNow alapszintű SAML-konfiguráció** szakaszában lévő **azonosítóba** .

              d. Győződjön meg arról, hogy a **NameID szabályzat** `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` értéke értékre van állítva. 

              e. Kattintson a **speciális** elemre, és adja meg az **egyetlen Sign-On parancsfájl** értékét **MultiSSOv2_SAML2_customként**.

         1. Görgessen le az **X. 509 tanúsítvány** szakaszhoz, és válassza a **Szerkesztés** lehetőséget.

             ![Képernyőkép az X. 509 tanúsítványról, Kiemelt szerkesztési lehetőséggel](./media/servicenow-tutorial/tutorial-servicenow-09.png "Egyszeri bejelentkezés konfigurálása")

         1. Válassza ki a tanúsítványt, és kattintson a jobbra mutató nyíl ikonra a tanúsítvány hozzáadásához.

            ![Képernyőfelvétel a gyűjteményről, a tanúsítvány és a jobbra mutató nyíl ikon kiemelve](./media/servicenow-tutorial/tutorial-servicenow-11.png "Egyszeri bejelentkezés konfigurálása")

          1. Kattintson a **Mentés** gombra.

          1. A lap jobb felső sarkában válassza a **kapcsolatok tesztelése** lehetőséget.

             ![Képernyőkép az oldalról, a tesztelési kapcsolatok kiemelésével](./media/servicenow-tutorial/tutorial-activate-2.png "Beépülő modul aktiválása")

             > [!NOTE]
             > Ha a teszt-kapcsolódás sikertelen, és nem tudja aktiválni ezt a kapcsolódást, akkor a ServiceNow a felülbírálás kapcsolót is biztosítja. Sys_properties kell megadnia **.** Megjelenik a **Keresés navigációs sávon** , és megnyílik a Rendszertulajdonságok új lapja. Itt létre kell hoznia egy olyan új tulajdonságot, amelynek a neve **Glide. hitelesítés. multisso. test.** retesze. kötelező az **adattípus** értéke **TRUE/FALSE** , majd állítsa be az értéket **hamis** **értékre** .

             > ![Képernyőkép a kapcsolatok tesztelése lapról](./media/servicenow-tutorial/test-connection-fail.png "Egyszeri bejelentkezés konfigurálása")
        
          1. Ha a rendszer kéri a hitelesítő adatait, adja meg őket. A következő oldal jelenik meg. Az **SSO-Kijelentkezési A teszt eredményei** hibát várt. Hagyja figyelmen kívül a hibát, és kattintson az  **aktiválás** gombra.

             ![A hitelesítő adatok oldalának képernyőképe](./media/servicenow-tutorial/servicenow-activate.png "Egyszeri bejelentkezés konfigurálása")
  
1. A **ServiceNow** manuális konfigurálásához kövesse az alábbi lépéseket:

    1. Jelentkezzen be a ServiceNow alkalmazásba rendszergazdaként.

    1. A bal oldali ablaktáblán válassza az **identitás-szolgáltatók** elemet.

        ![Képernyőfelvétel a többszolgáltatós egyszeri bejelentkezésről](./media/servicenow-tutorial/tutorial-servicenow-07.png "Egyszeri bejelentkezés konfigurálása")

    1. Az **Identity Providers** párbeszédpanelen válassza az **új** lehetőséget.

        ![Képernyőkép az Identity Providers párbeszédpanelről, új kiemelve](./media/servicenow-tutorial/ic7694977.png "Egyszeri bejelentkezés konfigurálása")

    1. Az **Identity Providers** párbeszédpanelen válassza az **SAML** lehetőséget.

        ![Képernyőkép az Identity Providers párbeszédpanelről, az SAML kiemelve](./media/servicenow-tutorial/ic7694978.png "Egyszeri bejelentkezés konfigurálása")

    1. Az **identitás-szolgáltató metaadatainak importálása** területen hajtsa végre a következő lépéseket:

        ![Képernyőkép az identitás-szolgáltató metaadatainak importálásáról, URL-cím és az importálás kiemelve](./media/servicenow-tutorial/idp.png "Egyszeri bejelentkezés konfigurálása")

        1. Adja meg az **alkalmazás összevonási metaadatának URL-címét** , amelyet a Azure Portal másolt.

        1. Válassza az **Importálás** lehetőséget.

    1. Beolvassa a identitásszolgáltató metaadat-URL-címét, és feltölti az összes mező információit.

        ![A személyazonosság-szolgáltató képernyőképe](./media/servicenow-tutorial/ic7694982.png "Egyszeri bejelentkezés konfigurálása")

        a. A **név** mezőben adja meg a konfiguráció nevét (például **Microsoft Azure összevont egyszeri bejelentkezés**).

        b. Másolja a **ServiceNow-Kezdőlap** értékét. Illessze be a **bejelentkezési URL-cím** mezőbe a Azure Portal **ServiceNow ALAPszintű SAML-konfiguráció** szakaszában.

        > [!NOTE]
        > A ServiceNow-példány kezdőlapja a **ServiceNow-bérlő URL-címének** és **/navpage.do** összefűzése (például: `https://fabrikam.service-now.com/navpage.do` ).

        c. Másolja az **entitás azonosítója/kiállító** értékét. Illessze be az azonosítót a Azure Portal **ServiceNow alapszintű SAML-konfiguráció** szakaszában lévő **azonosítóba** .

        d. Győződjön meg arról, hogy a **NameID szabályzat** `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` értéke értékre van állítva.

        e. Válassza az **Advanced** (Speciális) lehetőséget. A **felhasználó mezőbe** írja be az **e-mail címet**.

        > [!NOTE]
        > Az Azure AD úgy is beállítható, hogy az Azure AD felhasználói AZONOSÍTÓját (egyszerű felhasználónév) vagy az e-mail-címet az SAML-jogkivonatban egyedi azonosítóként bocsátja ki. Ehhez nyissa meg a Azure Portal **ServiceNow**  >    >  **-attribútumok egyszeri bejelentkezés** szakaszát, és rendelje hozzá a kívánt mezőt a **NameIdentifier** attribútumhoz. Az Azure AD kiválasztott attribútumának (például az egyszerű felhasználónév) értékének meg kell egyeznie a megadott mező ServiceNow (például user_name) tárolt értékével.

        : Válassza az oldal jobb felső sarkában található **kapcsolatok tesztelése** lehetőséget.

        > [!NOTE]
        > Ha a teszt-kapcsolódás sikertelen, és nem tudja aktiválni ezt a kapcsolódást, akkor a ServiceNow a felülbírálás kapcsolót is biztosítja. Sys_properties kell megadnia **.** Megjelenik a **Keresés navigációs sávon** , és megnyílik a Rendszertulajdonságok új lapja. Itt létre kell hoznia egy olyan új tulajdonságot, amelynek a neve **Glide. hitelesítés. multisso. test.** retesze. kötelező az **adattípus** értéke **TRUE/FALSE** , majd állítsa be az értéket **hamis** **értékre** .

          > ![Képernyőkép a kapcsolatok teszteléséről](./media/servicenow-tutorial/test-connection-fail.png "Egyszeri bejelentkezés konfigurálása")

        h. Ha a rendszer kéri a hitelesítő adatait, adja meg őket. A következő oldal jelenik meg. Az **SSO-Kijelentkezési A teszt eredményei** hibát várt. Hagyja figyelmen kívül a hibát, és kattintson az  **aktiválás** gombra.

          ![hitelesítő adatok](./media/servicenow-tutorial/servicenow-activate.png "Egyszeri bejelentkezés konfigurálása")

### <a name="create-servicenow-test-user"></a>ServiceNow-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy B. Simon nevű felhasználó létrehozása a ServiceNow-ben. A ServiceNow támogatja az automatikus felhasználó-kiépítés szolgáltatást, amely alapértelmezés szerint engedélyezve van.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [ServiceNow-ügyfél támogatási csapatához](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>ServiceNow expressz SSO konfigurálása

1. Jelentkezzen be a ServiceNow Express alkalmazásba rendszergazdaként.

2. A bal oldali panelen válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Képernyőkép a ServiceNow Express alkalmazásról, egyetlen Sign-On kiemelve](./media/servicenow-tutorial/ic7694980ex.png "Alkalmazás URL-címének konfigurálása")

3. Az **egyszeri bejelentkezés** párbeszédpanelen válassza a jobb felső sarokban található konfiguráció ikont, és állítsa be a következő tulajdonságokat:

    ![Képernyőfelvétel egyetlen Sign-On párbeszédpanelről](./media/servicenow-tutorial/ic7694981ex.png "Alkalmazás URL-címének konfigurálása")

    a. Váltás a **több szolgáltató egyszeri bejelentkezésének engedélyezése** a jobb oldalon.

    b. A **többszörös szolgáltató egyszeri bejelentkezéses integrációjának hibakeresési naplózásának engedélyezése** a jobb oldalon.

    c. A **felhasználó tábla mezőjében** adja meg a következőt: **user_name**.

4. Az **egyszeri bejelentkezés** párbeszédpanelen válassza az **új tanúsítvány hozzáadása** lehetőséget.

    ![Képernyőfelvétel egyetlen Sign-On párbeszédpanelről, új tanúsítvány hozzáadásával](./media/servicenow-tutorial/ic7694973ex.png "Egyszeri bejelentkezés konfigurálása")

5. Az **X. 509 tanúsítványok** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Képernyőkép az X. 509 tanúsítványok párbeszédpanelről](./media/servicenow-tutorial/ic7694975.png "Egyszeri bejelentkezés konfigurálása")

    a. A **név** mezőben adja meg a konfiguráció nevét (például: **testsaml 2.0**).

    b. Válassza az **aktív** lehetőséget.

    c. A **Formátum** beállításnál válassza a **PEM** lehetőséget.

    d. A **Típus mezőben** válassza a **megbízható tároló-tanúsítvány** lehetőséget.

    e. Nyissa meg Azure Portal a Jegyzettömbben letöltött Base64 kódolású tanúsítványt. Másolja a tartalmát a vágólapra, majd illessze be a **PEM-tanúsítvány** szövegmezőbe.

    f. Válassza a **Frissítés** lehetőséget

6. Az **egyszeri bejelentkezés** párbeszédpanelen válassza az **új identitásszolgáltató hozzáadása** lehetőséget.

    ![Képernyőfelvétel egyetlen Sign-On párbeszédpanelről, az új identitásszolgáltató kiemelésével](./media/servicenow-tutorial/ic7694976ex.png "Egyszeri bejelentkezés konfigurálása")

7. Az **új identitás-szolgáltató hozzáadása** párbeszédpanelen az **Identitáskezelő konfigurálása** területen hajtsa végre a következő lépéseket:

    ![Képernyőkép az új identitás-szolgáltató hozzáadása párbeszédpanelről](./media/servicenow-tutorial/ic7694982ex.png "Egyszeri bejelentkezés konfigurálása")

    a. A **név** mezőben adja meg a konfiguráció nevét (például: **SAML 2,0**).

    b. Az **identitás-szolgáltató URL-címéhez** illessze be a Azure Portalból másolt Identity Provider azonosító értékét.

    c. Az **Identitáskezelő AuthnRequest** illessze be a Azure Portalból másolt hitelesítési kérelem URL-címének értékét.

    d. Az **Identitáskezelő SingleLogoutRequest** illessze be a Azure Portalból másolt kijelentkezési URL-cím értékét.

    e. Az **identitás-szolgáltató tanúsítványa** mezőben válassza ki az előző lépésben létrehozott tanúsítványt.

8. Válassza a **Speciális beállítások** lehetőséget. A **további identitás-szolgáltató tulajdonságai** területen hajtsa végre a következő lépéseket:

    ![Képernyőkép az új identitás-szolgáltató hozzáadása párbeszédpanelről, speciális beállításokkal kiemelve](./media/servicenow-tutorial/ic7694983ex.png "Egyszeri bejelentkezés konfigurálása")

    a. **A identitásszolgáltató SingleLogoutRequest tartozó protokollok kötéséhez** írja be az **urn: Oasis: Names: TC: SAML: 2.0: kötések: http-átirányítás** értéket.

    b. A **NameID** szabályzata mezőbe írja be az **urn: Oasis: Names: TC: SAML: 1.1: NameID-Format: unmegadott értéket**.

    c. A **AuthnContextClassRef metódusnál** adja meg a következőt: `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` .

    d. A **AuthnContextClass létrehozásához** kapcsolja ki a következőt: kikapcsolva (nincs kijelölve).

9. A **további szolgáltatói tulajdonságok** területen hajtsa végre a következő lépéseket:

    ![Képernyőkép az új identitás-szolgáltató hozzáadása párbeszédpanelről, különböző tulajdonságokkal kijelölve](./media/servicenow-tutorial/ic7694984ex.png "Egyszeri bejelentkezés konfigurálása")

    a. A **ServiceNow kezdőlapján** adja meg a ServiceNow-példány kezdőlapjának URL-címét.

    > [!NOTE]
    > A ServiceNow-példány kezdőlapja a **ServiceNow-bérlő URL-címének** és **/navpage.do** összefűzése (például: `https://fabrikam.service-now.com/navpage.do` ).

    b. Az **entitás azonosítója/kiállítója** mezőben adja meg az ServiceNow-bérlő URL-címét.

    c. A **célközönség URI-ja** mezőben adja meg az ServiceNow-bérlő URL-címét.

    d. Az **óra döntéséhez** adja meg a **60** értéket.

    e. A **felhasználó mezőben** adja meg az **e-mail címet**.

    > [!NOTE]
    > Az Azure AD úgy is beállítható, hogy az Azure AD felhasználói AZONOSÍTÓját (egyszerű felhasználónév) vagy az e-mail-címet az SAML-jogkivonatban egyedi azonosítóként bocsátja ki. Ehhez nyissa meg a Azure Portal **ServiceNow**  >    >  **-attribútumok egyszeri bejelentkezés** szakaszát, és rendelje hozzá a kívánt mezőt a **NameIdentifier** attribútumhoz. Az Azure AD kiválasztott attribútumának (például az egyszerű felhasználónév) értékének meg kell egyeznie a megadott mező ServiceNow (például user_name) tárolt értékével.

    f. Kattintson a **Mentés** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a ServiceNow csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a ServiceNow, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-servicenow-classic-mobile"></a>SSO tesztelése a klasszikus ServiceNow (mobil)

1. Nyissa meg a **ServiceNow klasszikus (mobil)** alkalmazását, és hajtsa végre a következő lépéseket:

    a. Kattintson a plusz jelre a jobb alsó sarokban.

    ![Képernyőfelvétel a klasszikus ServiceNow alkalmazásról, valamint a Kiemelt jellel](./media/servicenow-tutorial/test-03.png)

    b. Adja meg a ServiceNow-példány nevét, és kattintson a **Continue (folytatás**) gombra.

    ![Képernyőkép a példány hozzáadása oldalról, a folytatás kiemelve](./media/servicenow-tutorial/test-04.png)

    c. A **Bejelentkezés** oldalon hajtsa végre a következő lépéseket:

    ![Képernyőkép a bejelentkezés oldalról, külső bejelentkezés használata kiemelve](./media/servicenow-tutorial/test-01.png)

    *  Adja meg a **felhasználónevet**, például: B.simon@contoso.com .

    *  Válassza a **külső bejelentkezés használata** lehetőséget. A rendszer átirányítja az Azure AD-lapra a bejelentkezéshez.

    *  Adja meg hitelesítő adatait. Ha van harmadik féltől származó hitelesítés, vagy bármely más biztonsági funkció engedélyezve van, a felhasználónak ennek megfelelően kell válaszolnia. Megjelenik az alkalmazás **kezdőlapja** .

        ![Az alkalmazás kezdőlapjának képernyőképe](./media/servicenow-tutorial/test-02.png)

## <a name="next-steps"></a>Következő lépések

A ServiceNow konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáféréstől. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
