---
title: 'Oktatóanyag: Azure Active Directory integráció a Atlassian-felhővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Atlassian-felhő között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: fe83a012cac68f705bc9fabc7748f5a7c7c61bbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94330532"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Oktatóanyag: a Atlassian-felhő integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Atlassian-felhőt Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Atlassian-felhőt, a következőket teheti:

* A Atlassian-felhőhöz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Atlassian felhőbe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos ingyenes próbaverziót.
* Atlassian-alapú Felhőbeli egyszeri bejelentkezés (SSO) engedélyezett előfizetés.
* A Atlassian Cloud Products szolgáltatáshoz való Security Assertion Markup Language (SAML) egyszeri bejelentkezés engedélyezéséhez be kell állítania a Atlassian-hozzáférést. További információ a [Atlassian-hozzáférésről]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. 

* A Atlassian Cloud támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A Atlassian Cloud támogatja [a felhasználók automatikus üzembe](atlassian-cloud-provisioning-tutorial.md) helyezését és megszüntetését

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Atlassian-felhő hozzáadása a gyűjteményből

A Atlassian Cloud Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Atlassian-felhőt a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Atlassian Cloud** kifejezést a keresőmezőbe.
1. Válassza a **Atlassian Cloud** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD SSO konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Atlassian-felhővel egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Atlassian-felhőben.

Az Azure AD SSO Atlassian-felhővel való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad konfigurálása a Atlassian Cloud SSO](#configure-azure-ad-sso)** használatával – lehetővé teszi, hogy a felhasználók az Azure ad-alapú SAML SSO-t használják a Atlassian-felhővel.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Hozzon létre egy Atlassian Cloud test User](#create-atlassian-cloud-test-user)** -t, hogy a Atlassian-felhőben található B. Simon párja legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Atlassian-felhőben lévő konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, a **Atlassian-felhő beállítása** elemre kattintva megnyithatja a Atlassian Cloud alkalmazást. Itt adja meg a rendszergazdai hitelesítő adatokat a Atlassian-felhőbe való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a Atlassian-felhőt, jelentkezzen be a Atlassian Cloud vállalati webhelyre rendszergazdaként, és hajtsa végre az alábbi lépéseket.

1. Mielőtt elkezdené a Atlassian, és másolja/mentse a példány URL-címét
   > [!NOTE]
   > az URL-címnek illeszkedő `https://<instancename>.atlassian.net` minta

   ![példány neve](./media/atlassian-cloud-tutorial/get-atlassian-instance-name.png)

1. Nyissa meg a [Atlassian felügyeleti portált](https://admin.atlassian.com/) , és kattintson a szervezet nevére

   ![szervezet](./media/atlassian-cloud-tutorial/click-on-organization-in-atlassian-access.png)

1. Az egyszeri bejelentkezés konfigurálása előtt ellenőriznie kell a tartományt. További információ: [Atlassian tartomány-ellenőrzési](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) dokumentum.
1. A Atlassian felügyeleti portál képernyőjén válassza a **Biztonság** lehetőséget a bal oldali navigációs ablakban.

   ![biztonság](./media/atlassian-cloud-tutorial/click-on-security-in-atlassian-access.png)

1. A Atlassian felügyeleti portál biztonsági képernyőjén válassza az **SAML egyszeri bejelentkezés** lehetőséget a bal oldali navigációs ablakban

   ![SAML SSO](./media/atlassian-cloud-tutorial/click-on-saml-sso-in-atlassian-access-security.png)

1. Kattintson az **SAML-konfiguráció hozzáadása** elemre, és hagyja megnyitva a lapot

   ![SAML-konfiguráció hozzáadása](./media/atlassian-cloud-tutorial/saml-configuration-in-atlassian-access-security-saml-sso.png)

   ![SAML-konfiguráció hozzáadása 2](./media/atlassian-cloud-tutorial/add-saml-configuration.png)

1. A Azure Portal a **Atlassian Cloud** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés beállítása** lehetőséget.

   ![egyszeri bejelentkezés beállítása](./media/atlassian-cloud-tutorial/set-up-sso.png)

1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.

   ![SAML az Azure-ban](./media/atlassian-cloud-tutorial/saml-in-azure.png)

1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon görgessen le a **Atlassian-felhő beállításához**
   
   a. Kattintson a **konfigurációs URL-címek** elemre.

   ![URLs](./media/atlassian-cloud-tutorial/configuration-urls.png)
   
   b. Másolja az **Azure ad-azonosító** értékét a Azure Portalból, illessze be az **Identity Provider entitás-azonosító** szövegmezőbe a Atlassian
   
   c. Másolja a **bejelentkezési URL-címet** a Azure Portalból, illessze be az **Identity Provider SSO URL** szövegmezőbe a Atlassian

   ![Identitás-szolgáltató egyszeri bejelentkezésének URL-címe](./media/atlassian-cloud-tutorial/configuration-urls-azure.png)

   ![entitás azonosítója és SS](./media/atlassian-cloud-tutorial/entity-id-and-ss.png)

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

   ![aláíró tanúsítvány](./media/atlassian-cloud-tutorial/certificate.png)

   ![1. tanúsítvány](./media/atlassian-cloud-tutorial/certificate-1.png)

1. SAML-konfiguráció **hozzáadása/mentése** a Atlassian-ben

1. Ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, szerkessze az **egyszeri Sign-On beállítása az SAML-vel** az Azure-ban beállítás **alapszintű** SAML-konfiguráció szakaszát, és nyissa meg az **SAML egyszeri bejelentkezési oldalt** a Atlassian felügyeleti portálon

   a. Másolja az **SP-entitás azonosítójának** értékét a Atlassian-ből, illessze be az Azure **azonosító (Entity ID)** mezőjébe, és állítsa be alapértelmezettként.
   
   b. Másolja az SP-t a **fogyasztói szolgáltatás URL-** címének értéke Atlassian, illessze be a **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** szövegmezőbe az Azure-ban, és állítsa be alapértelmezettként
   
   c. Másolja ki a **példány URL-címét** , amelyet az 1. lépésben másolt, majd illessze be az Azure **Relay State (továbbítási állapot** ) mezőjébe

   ![URL-címek másolása](./media/atlassian-cloud-tutorial/copy-urls.png)

   ![Szerkesztés gomb](./media/atlassian-cloud-tutorial/edit-button.png)

   ![URL-cím képe](./media/atlassian-cloud-tutorial/urls.png)
   
1. Ha az alkalmazást **SP** -ben kezdeményezett módban kívánja konfigurálni, szerkessze az Azure-beli SAML-oldal **Sign-On beállításának** **alapszintű SAML-konfiguráció** szakaszát. Másolja a **példány URL-címét** (az 1. lépésből), és illessze be a **bejelentkezési URL-cím** mezőbe az Azure-ban

   ![Szerkesztés gomb az URL-címekben](./media/atlassian-cloud-tutorial/edit-button.png)

   ![bejelentkezési URL-cím](./media/atlassian-cloud-tutorial/sign-on-URL.png)
   
1. A Atlassian Cloud Application egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az attribútum-hozzárendelés szerkesztéséhez kattintson a **Szerkesztés** ikonra. 

   ![attribútumok](./media/atlassian-cloud-tutorial/edit-attribute.png)
   
   1. Attribútum-hozzárendelés egy Microsoft 365 licenccel rendelkező Azure AD-bérlőhöz
      
      a. Kattintson az **egyedi felhasználói azonosítóra (Name ID)** vonatkozó jogcímre

      ![attribútumok és jogcímek](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. A Atlassian Cloud elvárja, hogy a **NameIdentifier** (**egyedi felhasználói azonosító**) a felhasználó e-mail-címére legyen leképezve (**User.email**). Szerkessze a **forrás attribútumot** , és változtassa meg a **User. mail**-ben. Mentse a jogcím módosításait.

      ![egyedi felhasználói azonosító](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. A végső attribútum-hozzárendeléseknek a következőképpen kell kinéznie.

      ![2. rendszerkép](./media/atlassian-cloud-tutorial/default-attributes-1.png)
      
   1. Attribútumok leképezése Microsoft 365 licenc nélküli Azure AD-bérlőhöz 

      a. Kattintson a `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` jogcímre.

      ![3. rendszerkép](./media/atlassian-cloud-tutorial/email-address.png)
         
      b. Míg az Azure nem tölti fel a **User. mail** attribútumot az Azure ad-bérlőben létrehozott felhasználók számára Microsoft 365 licencek nélkül, és az e-maileket a **userPrincipalName** attribútumban tárolja az ilyen felhasználók számára. A Atlassian Cloud elvárja, hogy a **NameIdentifier** (**egyedi felhasználói azonosító**) a felhasználó e-mail-címére legyen leképezve (**User. userPrincipalName**).  Szerkessze a **forrás attribútumot**  , és módosítsa a **User. userPrincipalName** értékre. Mentse a jogcím módosításait.

      ![e-mail beállítása](./media/atlassian-cloud-tutorial/set-email.png)
         
      c. A végső attribútum-hozzárendeléseknek a következőképpen kell kinéznie.

      ![4. rendszerkép](./media/atlassian-cloud-tutorial/default-attributes-2.png)
     
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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Atlassian-felhőhöz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Atlassian-felhő** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-atlassian-cloud-test-user"></a>Atlassian Cloud test-felhasználó létrehozása

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezzenek a Atlassian-felhőbe, a következő módon helyezze üzembe a felhasználói fiókokat manuálisan a Atlassian-felhőben:

1. Az **Adminisztráció** ablaktáblán válassza a **felhasználók** lehetőséget.

    ![A Atlassian Cloud Users hivatkozás](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Ha felhasználót szeretne létrehozni a Atlassian-felhőben, válassza a **felhasználó meghívása** lehetőséget.

    ![Atlassian felhőalapú felhasználó létrehozása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. Az **e-mail cím** mezőbe írja be a felhasználó e-mail-címét, majd rendelje hozzá az alkalmazáshoz való hozzáférést.

    ![Atlassian Felhőbeli felhasználó](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Ha e-mailben szeretne meghívót küldeni a felhasználónak, válassza a **felhasználók meghívása** lehetőséget. A rendszer elküld egy e-mailes meghívót a felhasználónak, és a meghívót elfogadva a felhasználó aktív a rendszeren.

> [!NOTE]
> A felhasználókat a **tömeges létrehozás** gomb kiválasztásával is létrehozhatja a **felhasználók** szakaszban.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Atlassian Felhőbeli bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a Atlassian Cloud bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Atlassian-felhőbe, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft Access panel használatával bármilyen módban tesztelheti az alkalmazást. Ha a hozzáférési panelen a Atlassian felhő csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra a Atlassian-felhőbe, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Következő lépések

A Atlassian-felhő konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).