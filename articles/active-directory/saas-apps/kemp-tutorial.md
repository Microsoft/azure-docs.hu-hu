---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Kemp LoadMaster Azure AD-integrációval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Kemp LoadMaster Azure AD-integrációja között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f42e1731-0477-4955-9571-b69a0f3de0bc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd436462d157a7e416f0f6a468edd33520ccec7d
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009928"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-kemp-loadmaster-azure-ad-integration"></a>Oktatóanyag: Azure Active Directory SSO-integráció a Kemp LoadMaster Azure AD-integrációval

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Kemp LoadMaster Azure AD-integrációt Azure Active Directory (Azure AD-val). Ha a Kemp LoadMaster Azure AD-integrációját az Azure AD-be integrálja, a következőket teheti:

* A Kemp LoadMaster Azure AD-integrációhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Kemp LoadMaster Azure AD-integrációba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Kemp LoadMaster az Azure AD-integrációs egyszeri bejelentkezés (SSO) engedélyezett előfizetést.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Kemp LoadMaster Azure AD-integrációja támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* Miután konfigurálta a Kemp LoadMaster az Azure AD-integrációt, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-kemp-loadmaster-azure-ad-integration-from-the-gallery"></a>Kemp-LoadMaster hozzáadása az Azure AD-integrációhoz a katalógusból

A Kemp LoadMaster Azure ad-integráció az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Kemp LoadMaster Azure AD-integrációt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **Kemp Loadmaster Azure ad Integration** in the Search Box.
1. Válassza a **Kemp Loadmaster Azure ad-integráció** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-kemp-loadmaster-azure-ad-integration"></a>Az Azure AD SSO konfigurálása és tesztelése a Kemp LoadMaster Azure AD-integrációhoz

Konfigurálja és tesztelje az Azure AD SSO-t a Kemp LoadMaster Azure AD-integrációval egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Kemp LoadMaster Azure AD-integrációban.

Az Azure AD SSO és a Kemp LoadMaster Azure AD-integrációjának konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Kemp Loadmaster Azure ad Integration SSO konfigurálása](#configure-kemp-loadmaster-azure-ad-integration-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.

1. **[Webkiszolgáló közzététele](#publishing-web-server)**
    1. **[Virtuális szolgáltatás létrehozása](#create-a-virtual-service)**
    1. **[Tanúsítványok és biztonság](#certificates-and-security)**
    1. **[Kemp LoadMaster Azure AD-integrációs SAML-profil](#kemp-loadmaster-azure-ad-integration-saml-profile)**
    1. **[Ellenőrizze a változtatásokat](#verify-the-changes)**
1. **[Kerberos-alapú hitelesítés konfigurálása](#configuring-kerberos-based-authentication)**
    1. **[Kerberos-delegálási fiók létrehozása a Kemp LoadMaster Azure AD-integrációhoz](#create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration)**
    1. **[Kemp LoadMaster Azure AD-integrációs KCD (Kerberos-delegálási fiókok)](#kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts)**
    1. **[Kemp LoadMaster Azure AD-integrációs ESP](#kemp-loadmaster-azure-ad-integration-esp)**
    1. **[Hozzon létre Kemp Loadmaster Azure ad-integrációs teszt felhasználót](#create-kemp-loadmaster-azure-ad-integration-test-user)** –, hogy a B. Simon, a Kemp LOADMASTER Azure ad-integrációja, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Kemp Loadmaster Azure ad Integration** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet:`https://<KEMP-CUSTOMER-DOMAIN>.com/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet:`https://<KEMP-CUSTOMER-DOMAIN>.com/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek beszerzéséhez lépjen kapcsolatba a [Kemp Loadmaster Azure ad Integration Client támogatási csapatával](mailto:support@kemp.ax) . Az Azure Portal alapszintű SAML-konfiguráció szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** és az **összevonási metaadatok XML**-fájlját, majd válassza a **Letöltés** lehetőséget a tanúsítvány és az összevonási metaadatok XML-fájljainak letöltéséhez, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](./media/kemp-tutorial/certificate-base-64.png)

1. A **Kemp Loadmaster Azure ad-integráció beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Kemp LoadMaster Azure AD-integrációs szolgáltatáshoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Kemp Loadmaster Azure ad-integráció**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-kemp-loadmaster-azure-ad-integration-sso"></a>A Kemp LoadMaster konfigurálása az Azure AD Integration SSO-hoz

## <a name="publishing-web-server"></a>Webkiszolgáló közzététele 

### <a name="create-a-virtual-service"></a>Virtuális szolgáltatás létrehozása 

1. Nyissa meg a Kemp LoadMaster Azure AD Integration Load Master webes felhasználói felületét > Virtual Services > új hozzáadása.

1. Kattintson az új hozzáadása gombra.

1. A virtuális szolgáltatás paramétereinek megadása.

    ![Kemp LoadMaster Azure AD-integrációs webkiszolgáló](./media/kemp-tutorial/kemp-1.png)

    a. Virtuális címe
    
    b. Port
    
    c. Szolgáltatás neve (nem kötelező)
    
    d. Protokoll 

1. Navigáljon a Real Servers (valódi kiszolgálók) szakaszhoz.

1. Kattintson az új hozzáadása gombra.

1. Határozza meg a valódi kiszolgáló paramétereit.
    
    ![Kemp LoadMaster Azure AD-integrációs webkiszolgáló](./media/kemp-tutorial/kemp-2.png)

    a. Válassza ki a távoli címek engedélyezése lehetőséget.
    
    b. Írja be a valódi kiszolgáló címe mezőbe
    
    c. Port
    
    d. Továbbítási módszer
    
    e. Tömeg
    
    f. Kapcsolatok korlátja
    
    : Kattintson a valódi kiszolgáló hozzáadása elemre.

## <a name="certificates-and-security"></a>Tanúsítványok és biztonság
 
### <a name="import-certificate-on-kemp-loadmaster-azure-ad-integration"></a>Tanúsítvány importálása a Kemp LoadMaster Azure AD-integrációra 
 
1. Nyissa meg a Kemp LoadMaster Azure AD Integration web Portal > tanúsítványokat & biztonsági > SSL-tanúsítványokat.

1. A tanúsítványok kezelése > a tanúsítvány konfigurálása területen.

1. Kattintson a tanúsítvány importálása elemre.

1. Adja meg a tanúsítványt tartalmazó fájl nevét. A fájl is rendelkezhet a titkos kulccsal. Ha a fájl nem tartalmazza a titkos kulcsot, akkor a titkos kulcsot tartalmazó fájlt is meg kell adni. A tanúsítvány lehet bármelyik. PEM vagy. PFX (IIS) formátum.

1. Kattintson a fájl kiválasztása a tanúsítványfájl elemre.

1. Kattintson a Key file (nem kötelező) elemre.

1. Kattintson a Save (Mentés) gombra.

### <a name="ssl-acceleration"></a>SSL-gyorsítás
 
1. Lépjen a Kemp Load Master webes felhasználói felület > Virtual Services > Virtual Services szolgáltatáshoz.

1. Kattintson a módosítás a művelet alatt elemre.

1. A TCP/x.x.x.: 443 (azonosító: 6) tulajdonságok alatt – a 7. rétegbeli operációs rendszer esetén kattintson az SSL-Tulajdonságok elemre.
    
    ![Kemp LoadMaster Azure AD-integrációs webkiszolgáló](./media/kemp-tutorial/kemp-3.png)
    
    a. Kattintson az Engedélyezve lehetőségre az SSL-gyorsításban.
    
    b. Az elérhető tanúsítványok területen válassza ki az importált tanúsítványt, és kattintson a `>` Symbol (szimbólum) elemre.
    
    c. Miután a kívánt SSL-tanúsítvány megjelenik a hozzárendelt tanúsítványokban, kattintson a **tanúsítványok beállítása**elemre.

    > [!NOTE]
    > Győződjön meg arról, hogy a **tanúsítványok beállítása**elemre kattint.

## <a name="kemp-loadmaster-azure-ad-integration-saml-profile"></a>Kemp LoadMaster Azure AD-integrációs SAML-profil
 
### <a name="import-idp-certificate"></a>Identitásszolgáltató-tanúsítvány importálása

Ugrás a Kemp LoadMaster Azure AD integrációs webkonzolra 

1. Kattintson a köztes tanúsítványok a tanúsítványok és a szolgáltató területen.

    ![Kemp LoadMaster Azure AD-integrációs webkiszolgáló](./media/kemp-tutorial/kemp-6.png)

    a. Kattintson az új köztes tanúsítvány hozzáadása lehetőségre a fájl kiválasztása elemre.
    
    b. Navigáljon az Azure AD Enterprise alkalmazásból korábban letöltött tanúsítványfájl.
    
    c. Kattintson a Megnyitás gombra.
    
    d. Adjon meg egy nevet a tanúsítvány nevében.
    
    e. Kattintson a tanúsítvány hozzáadása lehetőségre.

### <a name="create-authentication-policy"></a>Hitelesítési házirend létrehozása 
 
Nyissa meg az SSO kezelése virtuális szolgáltatások alatt lehetőséget.

   ![Kemp LoadMaster Azure AD-integrációs webkiszolgáló](./media/kemp-tutorial/kemp-7.png)
   
   a. A név megadása után kattintson az új ügyféloldali konfiguráció hozzáadása lehetőségre a Hozzáadás elemre.

   b. Válassza ki az SAML elemet a hitelesítési protokoll területen.

   c. Válassza ki a metaadat-fájlt a identitásszolgáltató kiépítés alatt. 

   d. Kattintson a fájl kiválasztása lehetőségre.

   e. Navigáljon a korábban letöltött XML-fájlból Azure Portal.

   f. Kattintson a Megnyitás gombra, és kattintson a identitásszolgáltató-Metaadatok importálása elemre.

   : Válassza ki a köztes tanúsítványt a identitásszolgáltató-tanúsítványból.

   h. Állítsa be az SP-entitás AZONOSÍTÓját, amelynek meg kell egyeznie a Azure Portalban létrehozott identitással. 

   i. Kattintson az SP-entitás AZONOSÍTÓjának beállítása elemre.

### <a name="set-authentication"></a>Hitelesítés beállítása  
 
A Kemp LoadMaster Azure AD-integrációs webkonzolján

1. Kattintson a Virtual Services elemre.

1. Kattintson a szolgáltatások megtekintése/módosítása elemre.

1. Kattintson a módosítás gombra, és navigáljon az ESP-beállításokhoz.
    
    ![Kemp LoadMaster Azure AD-integrációs webkiszolgáló](./media/kemp-tutorial/kemp-8.png)

    a. Kattintson az ESP engedélyezése lehetőségre.
    
    b. Válassza az SAML lehetőséget az ügyfél-hitelesítési módban.
    
    c. Válassza ki a korábban létrehozott ügyféloldali hitelesítést az SSO-tartományban.
    
    d. Írja be az állomásnév nevet az engedélyezett virtuális gazdagépek területen, majd kattintson az engedélyezett virtuális gazdagépek beállítása elemre.
    
    e. Írja be a/* értéket az engedélyezett virtuális könyvtárakban (hozzáférési követelmények alapján), majd kattintson az engedélyezett címtárak beállítása elemre.

### <a name="verify-the-changes"></a>Ellenőrizze a változtatásokat 
 
Keresse meg az alkalmazás URL-címét 

A nem hitelesített hozzáférés helyett a bérlőhöz tartozó bejelentkezési oldalt kell látnia. 

![Kemp LoadMaster Azure AD-integrációs webkiszolgáló](./media/kemp-tutorial/kemp-9.png)

## <a name="configuring-kerberos-based-authentication"></a>Kerberos-alapú hitelesítés konfigurálása 
 
### <a name="create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration"></a>Kerberos-delegálási fiók létrehozása a Kemp LoadMaster Azure AD-integrációhoz 

1. Hozzon létre egy felhasználói fiókot (ebben a példában AppDelegation).
    
    ![Kemp LoadMaster Azure AD-integrációs webkiszolgáló](./media/kemp-tutorial/kemp-10.png)


    a. Válassza az Attribútumszerkesztő lapot.

    b. Navigáljon a servicePrincipalName. 

    c. Válassza a servicePrincipalName elemet, majd kattintson a Szerkesztés gombra.

    d. A mező hozzáadásához írja be a http/kcduser értéket, és kattintson a Hozzáadás gombra. 

    e. Kattintson az alkalmaz, majd az OK gombra. Az ablaknak a megnyitása előtt be kell jelentkeznie (az új Delegálás lap megjelenítéséhez). 

1. Nyissa meg újra a felhasználó tulajdonságai ablakot, és a Delegálás lap elérhetővé válik. 

1. Válassza a Delegálás lapot.

    ![Kemp LoadMaster Azure AD-integrációs webkiszolgáló](./media/kemp-tutorial/kemp-11.png)

    a. Válassza A felhasználóval csak megadott szolgáltatások delegálhatók lehetőséget.

    b. Válassza a Bármely hitelesítési protokoll lehetőséget.

    c. Adja hozzá a valódi kiszolgálókat, és adja hozzá a http-t szolgáltatásként. 
    
    d. Jelölje be a kibontva jelölőnégyzetet. 

    e. Az összes kiszolgáló az állomásnévvel és a teljes tartománynévvel is megtekinthető.
    
    f. Kattintson az OK gombra.

> [!NOTE]
> Állítsa be az SPN-t az alkalmazáson vagy a webhelyen a megfelelő módon. Alkalmazás elérése az Alkalmazáskészlet identitásának beállításakor. Az IIS-alkalmazás FQDN-névvel való eléréséhez nyissa meg a valós kiszolgáló parancssorát, és írja be a SetSpn parancsot a szükséges paraméterekkel. Például: Setspn – S HTTP/sescoindc. sunehes. co. in suneshes\kdcuser 

### <a name="kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts"></a>Kemp LoadMaster Azure AD-integrációs KCD (Kerberos-delegálási fiókok) 

Nyissa meg a Kemp LoadMaster Azure AD-integrációs webkonzolját > Virtual Services > az egyszeri bejelentkezés kezelése.

![Kemp LoadMaster Azure AD-integrációs webkiszolgáló](./media/kemp-tutorial/kemp-12.png)

a. Navigáljon a kiszolgálóoldali egyszeri bejelentkezési konfigurációkhoz.

b. Írja be a nevet az új kiszolgálóoldali konfiguráció hozzáadása területen, majd kattintson a Hozzáadás gombra.

c. Válassza a Kerberos által korlátozott delegálás lehetőséget a hitelesítési protokollban.

d. Írja be a tartomány nevét a Kerberos tartományban.

e. Kattintson a Kerberos-tartomány beállítása elemre.

f. Írja be a tartományvezérlő IP-címét a Kerberos-kulcsszolgáltatóba.

: Kattintson a Kerberos KDC beállítása elemre.

h. Írja be a KCD felhasználónevet a Kerberos megbízható Felhasználónév mezőbe.

i. Kattintson a KDC megbízható Felhasználónév beállítása elemre.

j. Írja be a jelszót a Kerberos megbízható felhasználói jelszavában.

k. Kattintson a KCD megbízható felhasználói jelszó beállítása elemre.

### <a name="kemp-loadmaster-azure-ad-integration-esp"></a>Kemp LoadMaster Azure AD-integrációs ESP 

Válassza a Virtual Services > szolgáltatások megtekintése/módosítása lehetőséget.

![Kemp LoadMaster Azure AD-integrációs webkiszolgáló](./media/kemp-tutorial/kemp-13.png)

a. Kattintson a módosítás elemre a virtuális szolgáltatás nevére.
    
b. Kattintson az ESP-beállítások elemre.
    
c. A kiszolgálói hitelesítési mód területen válassza a KCD lehetőséget.
        
d. A kiszolgálóoldali konfiguráció területen válassza ki a korábban létrehozott kiszolgálóoldali profilt.

### <a name="create-kemp-loadmaster-azure-ad-integration-test-user"></a>Az Azure AD integrációs teszt felhasználójának létrehozása a Kemp LoadMaster

Ebben a szakaszban létrehoz egy B. Simon nevű felhasználót a Kemp LoadMaster Azure AD-integrációjában. A [Kemp Loadmaster Azure ad integrációs ügyfelének támogatási csapatával](mailto:support@kemp.ax) felveheti a felhasználókat a Kemp LOADMASTER Azure ad integrációs platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Kemp LoadMaster Azure AD-integráció csempére kattint, automatikusan be kell jelentkeznie a Kemp LoadMaster Azure AD-integrációba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Kemp LoadMaster Azure AD-integrációjának kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Kemp LoadMaster Azure AD-integrációjának a speciális láthatósággal és ellenőrzésekkel való ellátása](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
