---
title: 'Oktatóanyag: Azure Active Directory-integráció a Palo Alto Networks szolgáltatással – rendszergazdai felhasználói felület | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Palo Alto Networks – rendszergazdai felhasználói felület között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 57b1d47fa40c0af4bced1e4169fe60cd759ee2f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963638"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Oktatóanyag: Azure Active Directory a Palo Alto Networks-integrációval – rendszergazdai felhasználói felület

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Palo Alto Networks-admin felhasználói felületet Azure Active Directory (Azure AD) használatával.
A Palo Alto Networks integrálása – a rendszergazdai KEZELŐFELÜLET az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja a Palo Alto Networks-admin felhasználói felületét.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Palo Alto Networks-rendszergazdai felhasználói felületre (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a Palo Alto Networks-rendszergazdai felhasználói felülettel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Palo Alto Networks – rendszergazdai felhasználói felület egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Palo Alto Networks – a rendszergazdai KEZELŐFELÜLET támogatja az **SP** által kezdeményezett SSO-t
* Palo Alto Networks – a rendszergazda felhasználói FELÜLETe **a** felhasználók üzembe helyezését támogatja

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Palo Alto Networks – rendszergazdai felhasználói felület hozzáadása a katalógusból

A Palo Alto Networks-admin felhasználói felület az Azure AD-be való integrálásának konfigurálásához a gyűjteményből a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a Palo Alto Networks-admin felhasználói felületet.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **Palo Alto Networks-admin UI** a keresőmezőbe.
1. Válassza a **Palo Alto Networks – rendszergazdai felhasználói felület** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD SSO konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a Palo Alto Networks-admin felhasználói felületen konfigurálja és teszteli a **B. Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a hozzá tartozó, a Palo Alto hálózatokban található felhasználó közötti kapcsolati kapcsolat szükséges.

Az Azure AD egyszeri bejelentkezés a Palo Alto Networks-rendszergazdai KEZELŐFELÜLETtel való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Palo Alto Networks – rendszergazdai felhasználói felület egyszeri bejelentkezésének konfigurálása](#configure-palo-alto-networks---admin-ui-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Palo Alto-hálózatok létrehozása – rendszergazda felhasználói felület tesztelése](#create-palo-alto-networks---admin-ui-test-user)** – ha B. Simon-t ad hozzá a Palo Alto Networks – rendszergazdai kezelőfelülettel, amely a felhasználó Azure ad-képviseletéhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Palo Alto Networks-admin UI** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<Customer Firewall FQDN>/php/login.php`

    b. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. A **Válasz URL-címe** szövegmezőbe írja be a következő formátumban az előállítási szolgáltatás (ACS) URL-címét: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel, azonosítóval és válasz URL-címmel. Vegye fel a kapcsolatot a [Palo Alto Networks-admin felhasználói felület ügyfélszolgálatával](https://support.paloaltonetworks.com/support) , hogy lekérje ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.
    >
    > A 443-es port szükséges az **azonosítóhoz** és a **Válasz URL-címéhez** , mivel ezek az értékek a Palo Alto tűzfalon hardcoded. Ha eltávolítja a portszámot, a rendszer hibát okoz a bejelentkezés során.

    > A 443-es port szükséges az **azonosítóhoz** és a **Válasz URL-címéhez** , mivel ezek az értékek a Palo Alto tűzfalon hardcoded. Ha eltávolítja a portszámot, a rendszer hibát okoz a bejelentkezés során.

1. A Palo Alto Networks-admin felhasználói felületi alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

   > [!NOTE]
   > Mivel az attribútumok értékei csak példaként szolgálnak, a *Felhasználónév* és a *adminrole* megfelelő értékeit képezi le. Van egy másik opcionális attribútum is, a *accessdomain*, amely a tűzfal adott virtuális rendszereihez való rendszergazdai hozzáférés korlátozására szolgál.

1. A fentiek mellett a Palo Alto Networks-admin UI alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name |  Forrás attribútum|
    | --- | --- |
    | username | User. userPrincipalName |
    | adminrole | customadmin |
    | | |

    > [!NOTE]
    > A _adminrole_ értékének meg kell egyeznie a (z) 9. lépésben említett, a **Palo Alto hálózatokban** konfigurált szerepkör nevével. 

    > [!NOTE]
    > Az attribútumokkal kapcsolatos további információkért tekintse meg a következő cikkeket:
    > * [Rendszergazdai szerepkörű rendszergazda felhasználói felület (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Eszköz-hozzáférési tartomány felügyeleti felhasználói felülethez (accessdomain)](https://docs.paloaltonetworks.com/pan-os/8-0/pan-os-web-interface-help/device/device-access-domain.html)

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Palo Alto Networks-admin felhasználói felület beállítása** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelménynek megfelelően.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Palo Alto Networks-admin felhasználói felületéhez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Palo Alto Networks-admin felhasználói felület** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-palo-alto-networks---admin-ui-sso"></a>A Palo Alto Networks konfigurálása – rendszergazdai felhasználói felületi egyszeri bejelentkezés

1. Nyissa meg a Palo Alto Networks Firewall rendszergazdai felhasználói felületét rendszergazdaként egy új ablakban.

2. Válassza az **eszköz** fület.

    ![Az eszköz lap](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. A bal oldali ablaktáblán válassza ki az **SAML-identitás szolgáltatója** elemet, majd az **Importálás** elemre kattintva importálja a metaadat-fájlt.

    ![A Metaadatok importálása gomb](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Az **SAML azonosító szolgáltatói kiszolgáló profiljának importálása** ablakban tegye a következőket:

    ![A "SAML-azonosító a szolgáltatói kiszolgáló profiljának importálása" ablak](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. A **profil neve** mezőben adjon meg egy nevet (például **AzureAD felügyeleti felhasználói felület**).

    b. Az **Identity Provider metaadatai** területen válassza a **Tallózás** lehetőséget, majd válassza ki a Azure Portal korábban letöltött metadata.xml fájlt.

    c. Törölje az **identitás-szolgáltatói tanúsítvány** ellenőrzése jelölőnégyzet jelölését.

    d. Válassza az **OK** lehetőséget.

    e. Ha a tűzfalon szeretné véglegesíteni a konfigurációkat **, válassza a** végrehajtás lehetőséget.

5. A bal oldali ablaktáblán válassza ki az **SAML-identitás szolgáltatója** elemet, majd válassza ki az SAML identitás-szolgáltatói profilt (például **AzureAD felügyeleti felhasználói felület**), amelyet az előző lépésben hozott létre.

    ![Az SAML-identitás szolgáltatójának profilja](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. A **SAML Identity Provider kiszolgáló profilja** ablakban tegye a következőket:

    ![A "SAML identitás-szolgáltatói kiszolgáló profilja" ablak](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Az **Identity Provider slo URL-címe** mezőben cserélje le a korábban importált slo URL-címet a következő URL-címre: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Válassza az **OK** lehetőséget.

7. A Palo Alto Networks tűzfal rendszergazdai felhasználói felületén válassza az **eszköz** lehetőséget, majd válassza a **rendszergazdai szerepkörök** lehetőséget.

8. Kattintson a **Hozzáadás** gombra.

9. A rendszergazda **szerepkör profilja** ablakban a **név** mezőbe írja be a rendszergazdai szerepkör nevét (például **fwadmin**). A rendszergazdai szerepkör nevének meg kell egyeznie az identitás-szolgáltató által elindított SAML-rendszergazdai szerepkör attribútum nevével. A rendszergazdai szerepkör neve és értéke a Azure Portal **felhasználói attribútumok** szakaszában lett létrehozva.

    ![A Palo Alto Networks-rendszergazda szerepkör konfigurálása](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. A tűzfal rendszergazdai felhasználói felületén válassza az **eszköz** lehetőséget, majd válassza a **hitelesítési profil** lehetőséget.

11. Kattintson a **Hozzáadás** gombra.

12. A **hitelesítési profil** ablakban tegye a következőket: 

    ![A "hitelesítési profil" ablak](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. A **név** mezőben adjon meg egy nevet (például **AzureSAML_Admin_AuthProfile**).

    b. A **típus** legördülő listában válassza az **SAML** elemet. 

    c. A **identitásszolgáltató-kiszolgáló profilja** legördülő listában válassza ki a megfelelő SAML Identity Provider-kiszolgáló profilt (például **AzureAD felügyeleti felhasználói felület**).

    c. Jelölje be az **egyszeri kijelentkezés engedélyezése** jelölőnégyzetet.

    d. Az **adminisztrátori szerepkör attribútuma** mezőben adja meg az attribútum nevét (például **adminrole**).

    e. Válassza a **speciális** fület, majd az **engedélyezési lista** területen válassza a **Hozzáadás** lehetőséget.

    ![A Hozzáadás gomb a Speciális lapon](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Jelölje be az **összes** jelölőnégyzetet, vagy válassza ki azokat a felhasználókat és csoportokat, akikkel hitelesíteni lehet a profilt.  
    Amikor egy felhasználó hitelesíti magát, a tűzfal megfelel a társított felhasználónévnek vagy csoportnak a listában szereplő bejegyzéseknek. Ha nem ad hozzá bejegyzéseket, akkor egyetlen felhasználó sem tud hitelesíteni.

    : Válassza az **OK** lehetőséget.

13. Ha engedélyezni szeretné, hogy a rendszergazdák az SAML SSO-t használják az Azure-ban, válassza az **eszköz**  >  **beállítása** lehetőséget. A **telepítés** ablaktáblán válassza a **felügyelet** fület, majd a **hitelesítési beállítások** területen válassza a **Beállítások** ("fogaskerék") gombot.

    ![A beállítások gomb](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Válassza ki a hitelesítési profil ablakban létrehozott SAML-hitelesítési profilt (például **AzureSAML_Admin_AuthProfile**).

    ![A hitelesítési profil mező](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Válassza az **OK** lehetőséget.

16. A konfiguráció elvégzéséhez válassza a **commit (véglegesítés**) lehetőséget.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Palo Alto-hálózatok létrehozása – rendszergazda felhasználói felület tesztelése

Palo Alto hálózatok – a rendszergazda KEZELŐFELÜLETe támogatja az igény szerinti felhasználói üzembe helyezést. Ha a felhasználó még nem létezik, a rendszer automatikusan létrehozza a sikeres hitelesítés után. A felhasználó létrehozásához nincs szükség beavatkozásra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez a művelet átirányítja a Palo Alto Networks-admin felhasználói felület bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Nyissa meg a Palo Alto Networks-rendszergazdai felhasználói felület bejelentkezési URL-címét, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Palo Alto Networks-admin felhasználói felület csempére kattint, automatikusan be kell jelentkeznie a Palo Alto Networks-admin felhasználói felületére, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a Palo Alto Networks-admin felhasználói felületet, kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).