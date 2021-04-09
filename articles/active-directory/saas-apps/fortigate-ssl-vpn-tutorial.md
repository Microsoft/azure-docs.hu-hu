---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a FortiGate SSL VPN használatával | Microsoft Docs'
description: Megtudhatja, milyen lépéseket kell végrehajtania a FortiGate SSL VPN integrálásához Azure Active Directory (Azure AD) használatával.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2020
ms.author: jeedes
ms.openlocfilehash: 9852752799fd010ebb069637f55008d9c4f68bf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732110"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a FortiGate SSL VPN-lel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a FortiGate SSL VPN-t Azure Active Directory (Azure AD) használatával. Ha integrálja a FortiGate SSL VPN-t az Azure AD-vel, a következőket teheti:

* Az Azure AD használatával szabályozhatja, hogy ki férhet hozzá a FortiGate SSL VPN-hez.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az SSL VPN FortiGate az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* FortiGate SSL-alapú VPN-előfizetés egyszeri bejelentkezéssel (SSO).

## <a name="tutorial-description"></a>Oktatóanyag leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését tesztkörnyezetben végezheti el.

A FortiGate SSL VPN támogatja az SP által kezdeményezett egyszeri bejelentkezést.


## <a name="add-fortigate-ssl-vpn-from-the-gallery"></a>FortiGate SSL VPN hozzáadása a katalógusból

A FortiGate SSL VPN Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a FortiGate SSL VPN-t a katalógusból a felügyelt SaaS-alkalmazások listájára:

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók.
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban adja meg a **FortiGate SSL VPN** kifejezést a keresőmezőbe.
1. Válassza az **FORTIGATE SSL VPN** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Azure AD SSO konfigurálása és tesztelése a FortiGate SSL VPN-hez

Az Azure AD SSO konfigurálását és tesztelését a FortiGate SSL VPN használatával végezheti el egy B. Simon nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a megfelelő felhasználó között a FortiGate SSL VPN-ben.

Az Azure AD SSO FortiGate SSL VPN-sel való konfigurálásához és teszteléséhez hajtsa végre a következő magas szintű lépéseket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy engedélyezze a szolgáltatást a felhasználók számára.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez.
    1. **[Hozzáférés biztosítása a tesztelési felhasználónak](#grant-access-to-the-test-user)** az Azure ad egyszeri bejelentkezés engedélyezéséhez az adott felhasználó számára.
1. **[Konfigurálja a FORTIGATE SSL VPN SSO-](#configure-fortigate-ssl-vpn-sso)** t az alkalmazás oldalán.
    1. **Hozzon létre egy FORTIGATE SSL VPN-teszt felhasználót** a felhasználó Azure ad-beli képviseletéhez.
1. Ellenőrizze az **[SSO](#test-sso)** -t a konfiguráció működésének ellenőrzéséhez.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO-t a Azure Portalban:

1. A Azure Portal a **FORTIGATE SSL VPN-** alkalmazás integrációja oldalon, a **kezelés** szakaszban válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza gombot a beállítások szerkesztéséhez:

   ![Képernyőkép, amely az alapszintű SAML-konfiguráció szerkesztésére szolgáló ceruza gombot mutatja.](common/edit-urls.png)

1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon adja meg a következő értékeket:

    a. A **bejelentkezési URL-cím** mezőben adjon meg egy URL-címet a mintában `https://<FQDN>/remote/login` .

    b. Az **azonosító** mezőbe írjon be egy URL-címet a mintában `https://<FQDN>/remote/saml/metadata` .

    c. A **Válasz URL-címe** mezőbe írjon be egy URL-címet a mintában `https://<FQDN>/remote/saml/login` .

    d. A **kijelentkezési URL-cím** mezőben adjon meg egy URL-címet a mintában `https://<FQDN>/remote/saml/logout` .

    > [!NOTE]
    > Ezek az értékek csak minták. A tényleges **bejelentkezési URL-címet**, **azonosítót**, **Válasz URL**-címet és **kijelentkezési URL-** címet kell használnia. Útmutatásért forduljon a [Fortinet támogatási szolgálatához](https://support.fortinet.com) . A Azure Portal a Fortinet dokumentációjában és az **alapszintű SAML-konfiguráció** szakaszban látható példákat is megtekintheti.

1. A FortiGate SSL VPN-alkalmazás meghatározott formátumban várja az SAML-jogcímeket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia a konfigurációhoz. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Az alapértelmezett attribútumokat megjelenítő képernyőkép.](common/default-attributes.png)

1. A FortiGate SSL VPN által igényelt két további jogcím az alábbi táblázatban látható. A jogcímek nevének meg kell egyeznie az oktatóanyag **végrehajtás FortiGate parancssori konfiguráció** szakaszában használt nevekkel. 

   | Name |  Forrás attribútum|
   | ------------ | --------- |
   | username | User. userPrincipalName |
   | csoport | User. groups |
   
   A további jogcímek létrehozásához:

   a. A **felhasználói attribútumok & jogcímek** elem mellett válassza a **Szerkesztés** lehetőséget.

   b. Válassza az **új jogcím hozzáadása** lehetőséget.

   c. A **név** mezőben adja meg a **username** nevet.

   d. A **forrás attribútumnál** válassza a **User. userPrincipalName** elemet.

   e. Kattintson a **Mentés** gombra.

   f. Válassza **a csoport hozzáadása** lehetőséget.

   : Válassza a **Minden csoport** lehetőséget.

   h. Jelölje be a **csoportjogcím nevének testreszabása** jelölőnégyzetet.

   i. A **név** mezőbe írja be a következőt: **Group**.
   
   j. Kattintson a **Mentés** gombra.   

1. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **tanúsítvány (Base64)** melletti **Letöltés** hivatkozást a tanúsítvány letöltéséhez és a számítógépen való mentéséhez:

    ![A tanúsítvány letöltési hivatkozását megjelenítő képernyőkép.](common/certificatebase64.png)

1. A **FORTIGATE SSL VPN beállítása** szakaszban a követelmények alapján másolja a megfelelő URL-címet vagy URL-címeket:

    ![A konfigurációs URL-címeket megjelenítő képernyőkép.](common/copy-configuration-urls.png)

#### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget. Válassza a **felhasználók** lehetőséget, majd válassza **a minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre a következő lépéseket:
   1. A név mezőbe írja be a **B. Simon** **nevet** .  
   1. A **Felhasználónév** mezőbe írja be a nevet \<username> @ \<companydomain> . \<extension> Például: `B.Simon@contoso.com`.
   1. Válassza a **jelszó megjelenítése** lehetőséget, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

#### <a name="grant-access-to-the-test-user"></a>Hozzáférés biztosítása a tesztelési felhasználónak

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a felhasználónak a FortiGate SSL VPN-hez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **FORTIGATE SSL VPN** elemet.
1. Az alkalmazás Áttekintés lapjának **kezelés** szakaszában válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza az **B. Simon** elemet a **felhasználók** listán, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

#### <a name="create-a-security-group-for-the-test-user"></a>Biztonsági csoport létrehozása a tesztelési felhasználó számára

Ebben a szakaszban egy biztonsági csoportot hoz létre Azure Active Directory a tesztelési felhasználó számára. A FortiGate ezt a biztonsági csoportot fogja használni a VPN-en keresztüli felhasználói hálózati hozzáférés biztosításához.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget. Ezután válassza a **csoportok** lehetőséget.
1. Válassza az **új csoport** lehetőséget a képernyő tetején.
1. Az **új csoport** tulajdonságaiban hajtsa végre a következő lépéseket:
   1. A **csoport típusa** listában válassza a **Biztonság** elemet.
   1. A csoportnév mezőbe írja be a **FortiGateAccess** **nevet** .
   1. A **csoport leírása** mezőbe írja be **a FortiGate VPN-hozzáférés biztosítására szolgáló csoportot**.
   1. Az **Azure ad-szerepkörökhöz hozzá lehet rendelni a csoport (előzetes verzió)** beállításait, válassza a **nem** lehetőséget.
   1. A **tagság típusa** mezőben válassza a **hozzárendelt** elemet.
   1. A **tagok** területen válassza a **nincs kiválasztott tag** elemet.
   1. A **felhasználók és csoportok** párbeszédpanelen válassza a **felhasználók** listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
   1. Válassza a **Létrehozás** lehetőséget.
1. Miután visszatért a Azure Active Directory **csoportok** szakaszára, keresse meg a FortiGate- **hozzáférési** csoportot, és jegyezze fel az **objektumazonosítót**. Később szüksége lesz rá.

### <a name="configure-fortigate-ssl-vpn-sso"></a>FortiGate SSL VPN SSO konfigurálása

#### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>A Base64 SAML-tanúsítvány feltöltése a FortiGate készülékre

Miután végrehajtotta a FortiGate alkalmazás SAML-konfigurációját a bérlőn, letöltötte a Base64 kódolású SAML-tanúsítványt. Ezt a tanúsítványt fel kell töltenie a FortiGate készülékbe:

1. Jelentkezzen be a FortiGate-berendezés felügyeleti portálján.
1. A bal oldali ablaktáblán válassza a **System** elemet.
1. A **rendszerben** válassza a **tanúsítványok** lehetőséget.
1. Válassza a  >  **távoli tanúsítvány** importálása lehetőséget.
1. Tallózással keresse meg a FortiGate alkalmazás üzembe helyezéséről az Azure-bérlőn letöltött tanúsítványt, jelölje ki, majd kattintson **az OK gombra**.

A tanúsítvány feltöltése után jegyezze fel a nevét a   >  **rendszertanúsítványok**  >  **távoli tanúsítványa** alatt. Alapértelmezés szerint REMOTE_Cert_ *n* lesz, ahol *N* egész érték.

#### <a name="complete-fortigate-command-line-configuration"></a>FortiGate-parancssori konfiguráció befejezése

A következő lépésekhez az Azure kijelentkezési URL-címének konfigurálására van szükség. Ez az URL-cím kérdőjel karaktert (?) tartalmaz. A karakter sikeres elküldéséhez konkrét lépéseket kell elvégeznie. Ezek a lépések nem hajthatók végre a FortiGate CLI-konzolról. Ehelyett hozzon létre egy SSH-munkamenetet a FortiGate berendezéshez egy olyan eszköz használatával, mint a Putty. Ha az FortiGate-berendezés egy Azure-beli virtuális gép, a soros konzolon hajtsa végre az alábbi lépéseket az Azure-beli virtuális gépekhez.

A lépések elvégzéséhez szüksége lesz a korábban feljegyzett értékekre:

- Entitás azonosítója
- Válasz URL-cím
- Kijelentkezési URL-cím
- Azure bejelentkezési URL-cím
- Azure AD-azonosító
- Azure kijelentkezési URL-címe
- Base64 SAML-tanúsítvány neve (REMOTE_Cert_ *N*)

1. Hozzon létre egy SSH-munkamenetet a FortiGate készüléken, és jelentkezzen be egy FortiGate-rendszergazdai fiókkal.
1. Futtassa a következő parancsokat:

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > Az Azure kijelentkezési URL-címe tartalmaz egy `?` karaktert. Meg kell adnia egy speciális billentyűkombinációt, amely helyesen adja meg ezt az URL-címet a FortiGate soros konzolján. Az URL-cím általában `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .
   >
   > A soros konzolon az Azure kijelentkezési URL-címének megadásához írja be a következőt: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation` .
   > 
   > Ezután válassza a CTRL + V billentyűkombinációt, majd illessze be a többi URL-címet a sor befejezéséhez: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .

#### <a name="configure-fortigate-for-group-matching"></a>FortiGate konfigurálása csoport egyeztetéséhez

Ebben a szakaszban a FortiGate úgy konfigurálja, hogy felismerje a teszt felhasználót tartalmazó biztonsági csoport objektumazonosítót. Ez a konfiguráció lehetővé teszi a FortiGate számára a csoporttagság alapján történő hozzáférési döntések meghozatalát.

A lépések elvégzéséhez szüksége lesz az oktatóanyag korábbi részében létrehozott FortiGateAccess biztonsági csoport objektum-AZONOSÍTÓJÁRA.

1. Hozzon létre egy SSH-munkamenetet a FortiGate készüléken, és jelentkezzen be egy FortiGate-rendszergazdai fiókkal.
1. Futtassa a következő parancsokat:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

#### <a name="create-a-fortigate-vpn-portals-and-firewall-policy"></a>FortiGate VPN-portálok és tűzfal-házirend létrehozása

Ebben a szakaszban egy FortiGate VPN-portált és tűzfal-házirendet fog konfigurálni, amely hozzáférést biztosít az oktatóanyagban korábban létrehozott FortiGateAccess biztonsági csoporthoz.

A [FortiGate támogatási csapatával](mailto:tac_amer@fortinet.com) vegye fel a VPN-portálokat és a tűzfal-házirendet a FortiGate VPN-platformra. Az egyszeri bejelentkezés használata előtt el kell végeznie ezt a lépést.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a FortiGate VPN bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen a FortiGate VPN bejelentkezési URL-címére közvetlenül, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások FortiGate VPN-csempére kattint, a rendszer átirányítja a FortiGate VPN bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A FortiGate VPN konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)