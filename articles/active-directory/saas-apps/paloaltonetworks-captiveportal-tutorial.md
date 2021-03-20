---
title: 'Oktatóanyag: Azure Active Directory-integráció a Palo Alto Networks-szel kötött portálon | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést Azure Active Directory és a Palo Alto Networks-portál között.
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
ms.openlocfilehash: 1b9c9182b8ffc6e9ea2e05fd4863783f111f81dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98727149"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Oktatóanyag: Azure Active Directory-integráció a Palo Alto Networks-szel kötött portálon

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Azure Active Directory (Azure AD) használatával a Palo Alto Networks-portált.
A Palo Alto Networks-portál az Azure AD-vel való integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben felügyelheti a Palo Alto Networks-portálhoz való hozzáférést.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Palo Alto Networks-beli helyi portálra (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD és a Palo Alto Networks céges portál integrálásához a következő elemek szükségesek:

* Azure Active Directory előfizetés. Ha nem rendelkezik az Azure AD-vel, egy [hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/)is beszerezhet.
* A Palo Alto-hálózatok kötött portál egyszeri bejelentkezés (SSO) használatára képes előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Palo Alto Networks-portál támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A Palo Alto Networks-portálon a felhasználó üzembe helyezése **csak időben** támogatott

## <a name="adding-palo-alto-networks-captive-portal-from-the-gallery"></a>A-katalógusból származó Palo Alto Networks-portál hozzáadása

A Palo Alto Networks-portál az Azure AD-be való integrálásának konfigurálásához fel kell vennie a-katalógusban található Palo Alto Networks portált a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: a keresőmezőbe írja be a **Palo Alto Networks Portal** kifejezést.
1. Válassza ki a **Palo Alto Networks céges portált** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD SSO konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a Palo Alto Networks-portálon konfigurálhatja és tesztelheti egy **B. Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a hozzá tartozó, a Palo Alto Networks Portalon található kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés konfigurálásához és teszteléséhez a Palo Alto Networks céges portálon hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – engedélyezi a felhasználó számára a funkció használatát.
    * **[Azure ad-tesztkörnyezet létrehozása](#create-an-azure-ad-test-user)** – Azure ad egyszeri bejelentkezés a (z) B. Simon felhasználóval.
    * **[Rendelje hozzá az Azure ad-tesztelési felhasználót – a](#assign-the-azure-ad-test-user)** B. Simon beállítása az Azure ad egyszeri bejelentkezés használatára.
2. A **[Palo Alto Networks céges portál egyszeri bejelentkezésének konfigurálása](#configure-palo-alto-networks-captive-portal-sso)** – az alkalmazás egyszeri bejelentkezési beállításainak konfigurálása.
    * **[Hozzon létre egy Palo Alto Networks-beli céges portál tesztelési felhasználót](#create-a-palo-alto-networks-captive-portal-test-user)** –, hogy a B. Simon partnere legyen a Palo Alto Networks-beli helyi portálon, amely a felhasználó Azure ad-képviseletéhez van társítva.
3. **[SSO tesztelése](#test-sso)** – ellenőrizze, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Palo Alto Networks Portal** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** ablaktáblán hajtsa végre a következő lépéseket:

   1. Az **azonosító** mezőben adja meg a mintát tartalmazó URL-címet `https://<customer_firewall_host_name>/SAML20/SP` .

   2. A **Válasz URL-cím** mezőben adjon meg egy URL-címet, amely a mintát tartalmazta `https://<customer_firewall_host_name>/SAML20/SP/ACS` .

      > [!NOTE]
      > Az ebben a lépésben szereplő helyőrző értékek frissítése a tényleges azonosító és a válasz URL-címekkel. A tényleges értékek beszerzéséhez vegye fel a kapcsolatot a [Palo Alto Networks céges portál ügyfél-támogatási csapatával](https://support.paloaltonetworks.com/support).

5. Az **SAML aláíró tanúsítvány** szakaszban, az **összevonási metaadatok XML** elem mellett válassza a **Letöltés** lehetőséget. Mentse a letöltött fájlt a számítógépre.

    ![Az összevonási metaadatok XML-letöltési hivatkozása](common/metadataxml.png)

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Palo Alto Networks-portálhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza ki a **Palo Alto Networks céges portál** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-palo-alto-networks-captive-portal-sso"></a>A Palo Alto Networks céges portál egyszeri bejelentkezésének konfigurálása

Következő lépésként állítsa be az egyszeri bejelentkezést a Palo Alto-hálózatok helyi portálon:

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Palo Alto Networks webhelyre.

2. Válassza az **eszköz** fület.

    ![A Palo Alto Networks webhely eszköz lapja](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. A menüben válassza ki az **SAML-identitás szolgáltatója** elemet, majd válassza az **Importálás** lehetőséget.

    ![Az Importálás gomb](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. A **SAML-azonosító kiszolgáló profiljának importálása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![A Palo Alto Networks egyszeri bejelentkezés konfigurálása](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. A **profil neve** mezőben adjon meg egy nevet, például **AzureAD-CaptivePortal**.
    
    2. Az **Identitáskezelő metaadatainak** mellett válassza a **Tallózás** lehetőséget. Válassza ki a Azure Portalban letöltött metadata.xml fájlt.
    
    3. Válassza az **OK** lehetőséget.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Palo Alto Networks – kötött portál tesztelési felhasználó létrehozása

Ezután hozzon létre egy *Britta Simon* nevű felhasználót a Palo Alto Networks-portálon. A Palo Alto Networks-portál támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nem kell végrehajtania a feladatokat. Ha egy felhasználó még nem létezik a Palo Alto Networks-beli céges portálon, akkor a hitelesítés után létrejön egy újat.

> [!NOTE]
> Ha manuálisan szeretne felhasználót létrehozni, forduljon a [Palo Alto Networks céges portál ügyfél-támogatási csapatához](https://support.paloaltonetworks.com/support).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie a Palo Alto Networks-portálra, amelyhez be kell állítania az egyszeri bejelentkezést

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások területén található Palo Alto Networks Portal csempére kattint, automatikusan be kell jelentkeznie a Palo Alto Networks-portálra, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a Palo Alto Networks-beli céges portált, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).